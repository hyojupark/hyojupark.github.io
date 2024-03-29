---
title: Transformers 모델을 Triton으로 배포하기 (ONNX, TensorRT)
categories:
  - Triton
tags:
  - Transformers
  - BERT
  - Triton
  - ONNX
  - TensorRT
# published: false
---

일반적인 PyTorch 혹은 Transformers 모델 배포 성능을 극대화하기 위해 모델 포맷을 **ONNX**, **TensorRT**로 변환하고 **Triton Inference Server**로 배포하는 과정을 정리해봤습니다.

<br>

순서는 아래와 같습니다.
  1. 모델 학습
  2. ONNX 포맷 변환
  3. TensorRT 포맷 변환
  4. 배포 모델 구조 정의
  5. Trtion Inference Server 시작
  6. Inference
  7. Performance Analyzer


## 1. 모델 학습

우선 Transformers 모델을 학습시키겠습니다. BERT 기반 모델 중 하나인 `kcbert-large`([GitHub](https://github.com/Beomi/KcBERT))를 사용해서 감정분류(긍정, 중립, 부정) 모델을 학습시킵니다. 학습 코드는 짜로 작성하지 않았는데, 적당한 감정분석 데이터셋(ex. [금융 뉴스 문장 감성 분석 데이터셋](https://github.com/ukairia777/finance_sentiment_corpus))을 찾아서 하시면 됩니다.

학습에는 `pytorch/pytorch:1.11.0-cuda11.3-cudnn8-devel` 이미지에 `transformers 4.18.0` 패키지를 설치해서 사용했습니다. 

```python
# train.py
from transformers import AutoTokenizer, BertForSequenceClassification
import torch 
 
 
tokenizer = AutoTokenizer.from_pretrained("beomi/kcbert-large")
model = BertForSequenceClassification.from_pretrained("beomi/kcbert-large", num_labels=3).to('cuda')
 
...
 
tokenizer.save_pretrained('tokenizer_model')
torch.save(model.state_dict(), 'model.pt')
```

```dockerfile
# train.Dockerfile
FROM pytorch/pytorch:1.11.0-cuda11.3-cudnn8-devel

RUN pip install --upgrade pip
RUN pip install transformers==4.18.0 --no-cache-dir
```

```bash
$ docker build -t pytorch/pytorch:1.11.0-cuda11.3-cudnn8-devel-ap -f train.Dockerfile .
$ docker run -it -v $(pwd):/workspace --gpus=1 pytorch/pytorch:1.11.0-cuda11.3-cudnn8-devel-ap /bin/bash

...

root@server:/workspace# python train.py
```

학습한 모델을 저장할 때는 사용한 Tokenizer와 모델을 따로 저장했는데, Tokenizer는 뒤에 따로 사용할 예정이고 모델은 ONNX, TensorRT로 변환하기 쉽도록 PyTorch 모델로 저장할 때 `state_dict`를 함께 저장합니다.

<br>

## 2. ONNX 포맷 변환

저장한 PyTorch 모델(`model.pt`)을 ONNX 모델 포맷으로 변환합니다. 여기서 중요한 부분은 학습할 때 사용한 Tokenizer의 `max_length` 값을 input tensor의 length로 넣어주면 됩니다.

아래 코드는 `torch`와 `transformers`를 사용하기 때문에 위 학습에 사용된 이미지에서 실행하면 됩니다.

```python
# to_onnx.py
from transformers import AutoTokenizer, BertForSequenceClassification
import torch
import numpy as np
 
 
device = torch.device('cuda')
model = BertForSequenceClassification.from_pretrained("beomi/kcbert-large", num_labels=3).to(device)
model_path = 'model.pt'
model.load_state_dict(torch.load(model_path))
 
max_length = 300
input_ids = torch.as_tensor(np.ones([1, max_length]), dtype=torch.long).cuda()
attention_mask = torch.as_tensor(np.ones([1, max_length]), dtype=torch.long).cuda()
token_type_ids = torch.as_tensor(np.ones([1, max_length]), dtype=torch.long).cuda()
 
dynamic_axes = {
    'input_ids': {0 : 'batch_size'},
    'attention_mask': {0 : 'batch_size'},
    'token_type_ids': {0 : 'batch_size'},
    'outputs': {0 : 'batch_size'}
}
torch.onnx.export(
    model,
    (input_ids, attention_mask, token_type_ids),
    'model.onnx',
    input_names=['input_ids', 'attention_mask', 'token_type_ids'],
    output_names=['outputs'],
    dynamic_axes=dynamic_axes,
    opset_version=15)
```

```bash
$ docker run -it -v $(pwd):/workspace --gpus=1 pytorch/pytorch:1.11.0-cuda11.3-cudnn8-devel-ap /bin/bash

...

root@server:/workspace# python to_onnx.py
```

여기서는 PyTorch 모델 파일을 ONNX 모델 파일로 변환하기 위한 코드를 작성했지만, 사용하실 때는 위 학습코드 밑에 붙여서 PyTorch 모델 파일 생성 과정을 생략해도 상관없습니다.

<br>

## 3. TensorRT 포맷 변환

ONNX 모델(`model.onnx`)을 TensorRT 모델 포맷으로 변환할 때는 TensorRT의 `OnnxParser`를 이용해서 변환합니다. 때문에 TensorRT 이미지를 하나 가져와서 아래 코드를 실행시켜주면 됩니다. ONNX 변환할 때와 동일하게 `max_length`를 수정하면 되며, 이미지는 `nvcr.io/nvidia/tensorrt:23.05-py3`을 사용했습니다.

```python
# to_trt.py
import tensorrt as trt
 
onnx_file_name = 'model.onnx'
tensorrt_file_name = 'model.plan'
TRT_LOGGER = trt.Logger(trt.Logger.WARNING)
EXPLICIT_BATCH = 1 << (int)(trt.NetworkDefinitionCreationFlag.EXPLICIT_BATCH)
 
trt.init_libnvinfer_plugins(TRT_LOGGER, "")
 
builder = trt.Builder(TRT_LOGGER)
network = builder.create_network(EXPLICIT_BATCH)
parser = trt.OnnxParser(network, TRT_LOGGER)
 
config = builder.create_builder_config()
config.max_workspace_size = 1 << 30
 
config.set_flag(trt.BuilderFlag.FP16)
 
with open(onnx_file_name, 'rb') as model:
    if not parser.parse(model.read()):
        for error in range(parser.num_errors):
            print (parser.get_error(error))

max_length = 300
profile = builder.create_optimization_profile()
profile.set_shape('input_ids', (1, max_length), (1, max_length), (1, max_length))
profile.set_shape('attention_mask', (1, max_length), (1, max_length), (1, max_length))
profile.set_shape('token_type_ids', (1, max_length), (1, max_length), (1, max_length))
config.add_optimization_profile(profile)
 
#engine = builder.build_cuda_engine(network)
plan = builder.build_serialized_network(network, config)
with trt.Runtime(TRT_LOGGER) as runtime:
    engine = runtime.deserialize_cuda_engine(plan)
 
buf = engine.serialize()
with open(tensorrt_file_name, 'wb') as f:
    f.write(buf)
```

```bash
$ docker run -it v $(pwd):/workspace --gpus=1 nvcr.io/nvidia/tensorrt:23.05-py3 /bin/bash

...

root@server:/workspace# python to_trt.py
```

<br>

## 4. 배포 모델 구조 정의

여기부터가 Triton Inference Server로 모델을 배포하기 위한 과정의 시작입니다. 

### 4.1 모델 리포지토리 구조

배포하기 위해서는 모델과 설정 파일을 `model_repository` 구조에 맞춰 저장해야합니다. 여기에 Transformers 모델을 배포할 때는 <u>1) Tokenizer</u>와 <u>2) ONNX or TensorRT 모델</u>을 각각 정의하고 <u>3) 앙상블 모델</u>을 정의해서 배포해야합니다. 따라서 Transformers 모델 하나당 3개의 Triton 모델 정의가 필요하며, 여기서 ONNX와 TensorRT 모델 각각을 배포하면 아래와 같은 구조가 됩니다.

```bash
$ tree model_repository
model_repository/
├── kcbert_onnx
│   ├── 1
│   │   └── model.onnx
│   └── config.pbtxt
├── kcbert_onnx_ensemble
│   ├── 1
│   └── config.pbtxt
├── kcbert_trt_fp16
│   ├── 1
│   │   └── model.plan
│   └── config.pbtxt
├── kcbert_trt_fp16_ensemble
│   ├── 1
│   └── config.pbtxt
├── onnx_tokenizer
│   ├── 1
│   │   ├── model.py
│   │   ├── special_tokens_map.json
│   │   ├── tokenizer_config.json
│   │   ├── tokenizer.json
│   │   └── vocab.txt
│   └── config.pbtxt
└── trt_tokenizer
    ├── 1
    │   ├── model.py
    │   ├── special_tokens_map.json
    │   ├── tokenizer_config.json
    │   ├── tokenizer.json
    │   └── vocab.txt
    └── config.pbtxt
```

여기서는 ONNX의 Tokenizer, 모델, 앙상블 모델과 TensorRT의 Tokenizer, 모델, 앙상블 모델 총 6개의 모델을 각각 만들어야합니다.

### 4.2 모델 설정

#### 4.2.1 Tokenizer

Tokenizer 모델은 `Python backend`를 이용해서 모델이 아닌 Python 코드를 작성해서 동작시킵니다. 따라서 모델 파일 대신 앞서 모델 학습 과정에서 저장한 Toeknizer 파일(`special_tokens_map.json`, `tokenizer_config.json`, `tokenizer.json`, `vocab.txt`)과 `model.py` 파일에 코드를 작성해서 저장해줍니다.

```bash
onnx_tokenizer
├── 1
│   ├── model.py
│   ├── special_tokens_map.json
│   ├── tokenizer_config.json
│   ├── tokenizer.json
│   └── vocab.txt
└── config.pbtxt
```

```python
# model.py
import os
from typing import Dict, List
 
import numpy as np
import triton_python_backend_utils as pb_utils
from transformers import AutoTokenizer, PreTrainedTokenizer, TensorType
 
class TritonPythonModel:
    tokenizer: PreTrainedTokenizer
    max_length: int = 300
 
    def initialize(self, args: Dict[str, str]) -> None:
        """
        Initialize the tokenization process
        :param args: arguments from Triton config file
        """
        # more variables in https://github.com/triton-inference-server/python_backend/blob/main/src/python.cc
        path: str = os.path.join(args["model_repository"], args["model_version"])
        self.tokenizer = AutoTokenizer.from_pretrained(path)
 
    def execute(self, requests) -> "List[List[pb_utils.Tensor]]":
        """
        Parse and tokenize each request
        :param requests: 1 or more requests received by Triton server.
        :return: text as input tensors
        """
        responses = []
        # for loop for batch requests (disabled in our case)
        for request in requests:
            # binary data typed back to string
            query = [
                t.decode("UTF-8")
                for t in pb_utils.get_input_tensor_by_name(request, "input_text").as_numpy().tolist()
            ]
            tokens: Dict[str, np.ndarray] = self.tokenizer(
                query, 
                return_tensors=TensorType.NUMPY, 
                truncation=True, 
                padding='max_length', 
                add_special_tokens=True, 
                max_length=self.max_length
            )
 
            # tensorrt uses int32 as input type, ort uses int64
            tokens = {k: v.astype(np.int64) for k, v in tokens.items()}
            # communicate the tokenization results to Triton server
            outputs = list()
            for input_name in self.tokenizer.model_input_names:
                tensor_input = pb_utils.Tensor(input_name, tokens[input_name])
                outputs.append(tensor_input)
 
            inference_response = pb_utils.InferenceResponse(output_tensors=outputs)
            responses.append(inference_response)
 
        return responses
```

위 코드에서 중요한 부분은 `pb_utils.get_input_tensor_by_name` 함수와 tokenizer의 `max_length`, token의 자료형 변환인 `v.astype(np.int64)`입니다.

`pb_utils.get_input_tensor_by_name`를 통해서 triton으로 배포된 모델의 input을 받을 수 있습니다. 여기서는 단일 input만 생각하고 받도록 코드를 작성했습니다. 만약 batch 처리를 하려면 `t`를 배열로 받으면 됩니다.

`max_length`는 앞에서 계속 설명드렸던 Tokenizer의 값을 그대로 사용합니다.

`v.astype(np.int64)`는 모델 포맷이 **ONNX**인지 **TensorRT**인지에 따라 다릅니다. TensorRT는 Int64(Long) 타입을 지원하지 않기 때문에 Int32를 사용해야하고, ONNX는 Int64를 그대로 사용하면 됩니다.

이제 Tokenizer의 설정 파일(`config.pbtxt`)을 추가합니다.

```
# config.pbtxt
name: "onnx_tokenizer"
backend: "python"
max_batch_size: 0
 
input [
    {
        name: "input_text"
        data_type: TYPE_STRING
        dims: [-1]
    }
]
 
output [
    {
        name: "input_ids"
        data_type: TYPE_INT64
        dims: [-1, -1]
    },
    {
        name: "attention_mask"
        data_type: TYPE_INT64
        dims: [-1, -1]
    },
    {
        name: "token_type_ids"
        data_type: TYPE_INT64
        dims: [-1, -1]
    }
]
 
instance_group [
    {
      count: 1
      kind: KIND_GPU
    }
]
```

위 `config.pbtxt`도 동일하게 output의 data_type을 **ONNX**인 경우 `TYPE_INT64`로, **TensorRT**인 경우 `TYPE_INT32`로 설정하고 이름(`name`) 부분만 바꿔서 각각 설정하면 됩니다.

#### 4.2.2 모델

모델은 버전 디렉토리에 모델 파일(`model.onnx` or `model.plan`)을 넣고 설정 파일(`config.pbtxt`)을 작성하면 됩니다.

```bash
kcbert_onnx
├── 1
│   └── model.onnx
└── config.pbtxt
```

```
# ONNX의 config.pbtxt
name: "kcbert_onnx"
platform: "onnxruntime_onnx"
max_batch_size: 1
dynamic_batching {
  max_queue_delay_microseconds: 100
}
instance_group [
  {
    count: 1
    kind: KIND_GPU
  }
]
input [
  {
    name: "input_ids",
    data_type: TYPE_INT64
    dims: [ 300 ]
  },
  {
    name: "attention_mask",
    data_type: TYPE_INT64
    dims: [ 300 ]
  },
  {
    name: "token_type_ids",
    data_type: TYPE_INT64
    dims: [ 300 ]
  }
]
output [
  {
    name: "outputs",
    data_type: TYPE_FP32
    dims: [ 3 ]
  }
]
```

```bash
kcbert_trt_fp16
├── 1
│   └── model.plan
└── config.pbtxt
```

```
# TensorRT의 config.pbtxt
name: "kcbert_trt_fp16"
platform: "tensorrt_plan"
max_batch_size: 1
dynamic_batching {
  max_queue_delay_microseconds: 100
}
instance_group [
  {
    count: 1
    kind: KIND_GPU
  }
]
input [
  {
    name: "input_ids"
    data_type: TYPE_INT32
    dims: [ 300 ]
  },
  {
    name: "attention_mask"
    data_type: TYPE_INT32
    dims: [ 300 ]
  },
  {
    name: "token_type_ids"
    data_type: TYPE_INT32
    dims: [ 300 ]
  }
]
output [
  {
    name: "outputs",
    data_type: TYPE_FP32
    dims: [ 3 ]
  }
]
```

동일하게 input의 data_type을 **ONNX**는 `TYPE_INT64`, **TensorRT**는 `TYPE_INT32`로 작성하고 이름(`name`)과 플랫폼(`platform`) 부분을 프레임워크에 맞게 작성합니다.

#### 4.2.3 앙상블 모델

이제 input이 들어오면 Tokenizer를 거쳐 모델의 output까지 한번에 처리해줄 앙상블 모델을 작성합니다. 앙상블 모델은 모델없이 파이프라인만 정의하기 때문에 버전 디렉토리는 비우고 설정 파일(`config.pbtxt`)만 작성하면 됩니다.

```bash
kcbert_onnx_ensemble
├── 1
└── config.pbtxt
```

```
# ONNX Ensemble의 config.pbtxt
name: "kcbert_onnx_ensemble"
platform: "ensemble"
max_batch_size: 0
 
input [
    {
        name: "input_text"
        data_type: TYPE_STRING
        dims: [-1]
    }
]
output [
    {
        name: "outputs"
        data_type: TYPE_FP32
        dims: [ -1, 3 ]
    }
]
 
ensemble_scheduling {
    step [
        {
            model_name: "onnx_tokenizer"
            model_version: -1
            input_map {
                key: "input_text"
                value: "input_text"
            }
            output_map [
                {
                    key: "input_ids"
                    value: "input_ids"
                },
                {
                    key: "attention_mask"
                    value: "attention_mask"
                },
                {
                    key: "token_type_ids"
                    value: "token_type_ids"
                }
            ]
        },
        {
            model_name: "kcbert_onnx"
            model_version: -1
            input_map [
                {
                    key: "input_ids"
                    value: "input_ids"
                },
                {
                    key: "attention_mask"
                    value: "attention_mask"
                },
                {
                    key: "token_type_ids"
                    value: "token_type_ids"
                }
            ]
            output_map {
                key: "outputs"
                value: "outputs"
            }
        }
    ]
}
```

```bash
kcbert_trt_fp16_ensemble
├── 1
└── config.pbtxt
```

```
# TensorRT Ensemble의 config.pbtxt
name: "kcbert_trt_fp16_ensemble"
platform: "ensemble"
max_batch_size: 0
 
input [
    {
        name: "input_text"
        data_type: TYPE_STRING
        dims: [-1]
    }
]
output [
    {
        name: "outputs"
        data_type: TYPE_FP32
        dims: [ -1, 3 ]
    }
]
 
ensemble_scheduling {
    step [
        {
            model_name: "trt_tokenizer"
            model_version: -1
            input_map {
                key: "input_text"
                value: "input_text"
            }
            output_map [
                {
                    key: "input_ids"
                    value: "input_ids"
                },
                {
                    key: "attention_mask"
                    value: "attention_mask"
                },
                {
                    key: "token_type_ids"
                    value: "token_type_ids"
                }
            ]
        },
        {
            model_name: "kcbert_trt_fp16"
            model_version: -1
            input_map [
                {
                    key: "input_ids"
                    value: "input_ids"
                },
                {
                    key: "attention_mask"
                    value: "attention_mask"
                },
                {
                    key: "token_type_ids"
                    value: "token_type_ids"
                }
            ]
            output_map {
                key: "outputs"
                value: "outputs"
            }
        }
    ]
}
```

여기서는 이름(`name`)과 플랫폼(`platform`) 부분만 프레임워크에 맞게 작성합니다. 참고로 `model_version: -1`은 최신 모델을 가져오겠다는 의미입니다.

여기까지하면 모델 정의가 끝납니다. 이제 **Triton Inference Server**를 실행하면 됩니다.

<br>

## 5. Triton Inference Server 시작

이제 tritonserver 이미지를 만들어야합니다. 기본 이미지를 그냥 사용할수도 있지만, 위에 정의한 Tokenizer에서 사용하는 `transformers` 패키지 설치가 필요하기 때문에 Docker Image를 새로 빌드해줍니다.

```dockerfile
FROM nvcr.io/nvidia/tritonserver:23.04-py3
 
RUN pip install transformers==4.18.0
```

```bash
$ docker build -t nvcr.io/nvidia/tritonserver:23.04-py3-ap .
...
```

이제 Docker로 서버를 실행해주면 됩니다. 아래에서 사용되는 옵션 중 `--shm-size=1g`는 default가 `64m`밖에 안되기 때문에 모델을 여러 개 띄울 경우 오류가 발생하므로 넉넉하게 늘려주는게 좋습니다.
```bash
$ docker run --gpus=1 --shm-size=1g --rm -p 8000:8000 -p 8001:8001 -p 8002:8002 -v /home/user/triton-bert/model_repository:/models nvcr.io/nvidia/tritonserver:23.04-py3-ap tritonserver --model-repository=/models

...

+--------------------------+---------+--------+
| Model                    | Version | Status |
+--------------------------+---------+--------+
| kcbert_onnx              | 1       | READY  |
| kcbert_onnx_ensemble     | 1       | READY  |
| kcbert_trt_fp16          | 1       | READY  |
| kcbert_trt_fp16_ensemble | 1       | READY  |
| onnx_tokenizer           | 1       | READY  |
| trt_tokenizer            | 1       | READY  |
+--------------------------+---------+--------+

...

I1002 21:58:57.891440 62 grpc_server.cc:3914] Started GRPCInferenceService at 0.0.0.0:8001
I1002 21:58:57.893177 62 http_server.cc:2717] Started HTTPService at 0.0.0.0:8000
I1002 21:58:57.935518 62 http_server.cc:2736] Started Metrics Service at 0.0.0.0:8002
```

실행했을 때 위 처럼 6개 모델의 Status가 READY면 성공적으로 실행된 상태입니다.

<br>

## 6. Inference

이제 Inference를 해보겠습니다. 공식 GitHub에서 제공되는 대표 예제는 복잡한 코드의 gRPC 방식을 사용하기 때문에 여기서는 간단한 HTTP Request 방식을 사용해보도록 하겠습니다.

```python
import requests
 
 
#URL = 'http://localhost:8000/v2/models/kcbert_onnx_ensemble/infer'
URL = 'http://localhost:8000/v2/models/kcbert_trt_fp16_ensemble/infer'
 
data = {
    #'name': 'kcbert_onnx_ensemble',
    'name': 'kcbert_trt_fp16_ensemble',
    'inputs': [
        {
            'name': 'input_text',
            'shape': [1],
            'datatype': 'BYTES',
            'data': ['네이버 3분기 영업이익 상승으로 인한 주가 상승']
        }
    ]
}
 
resp = requests.post(URL, json=data)
print(resp.json())
```

```python
# ONNX Response
{'model_name': 'kcbert_onnx_ensemble', 'model_version': '1', 'parameters': {'sequence_id': 0, 'sequence_start': False, 'sequence_end': False}, 'outputs': [{'name': 'outputs', 'datatype   ': 'FP32', 'shape': [1, 3], 'data': [2.953115463256836, -0.5326197743415833, -3.386281967163086]}]}
 
# TensorRT Response
{'model_name': 'kcbert_trt_fp16_ensemble', 'model_version': '1', 'parameters': {'sequence_id': 0, 'sequence_start': False, 'sequence_end': False}, 'outputs': [{'name': 'outputs', 'data   type': 'FP32', 'shape': [1, 3], 'data': [2.93359375, -0.294677734375, -3.6796875]}]}
```

inference 방법은 위 코드처럼 URL을 작성하고 `name`과 `inputs`를 입력하면 됩니다. 모델의 output이 logits이기 때문에 outputs의 data에 logits가 있는 것을 확인할 수 있습니다.

<br>

## 7. Performance Analyzer

Triton Inference Server에서 제공되는 **Performance Analyzer**를 사용하면 <u>모델별 성능을 측정</u>해볼 수 있습니다. 

위에서 작성한 ONNX와 TensorRT 모델의 성능을 비교해볼건데, Performance Analyzer를 사용하기 위해서는 위의 1 dimension의 input을 2 dimension input으로 변경해줘야합니다. 단순 성능 측정만을 위해 변경하는 것이기 때문에 대충 변경해서 바로 확인해보겠습니다.

변경해줘야할 부분은 ONNX와 TensorRT 각 Tokenizer의 `model.py`와 `config.pbtxt`, 앙상블 모델의 `config.pbtxt`입니다.

```python
# model.py

    ...

    query = [
        t[0].decode("UTF-8")  # 수정
        for t in pb_utils.get_input_tensor_by_name(request, "input_text")
        .as_numpy()
        .tolist()
    ]

    ...
```

위 코드에서 `t.decode("UTF-8")`을 `t[0].decode("UTF-8")`로 변경해서 2 dimension input이지만 기존처럼 하나만 받아서 사용합니다.

```
# Tokenizer의 config.pbtxt
name: "onnx_tokenizer"
backend: "python"
max_batch_size: 0
 
input [
    {
        name: "input_text"
        data_type: TYPE_STRING
        dims: [-1, -1]  # 수정
    }
]
...
```

```
# 앙상블 모델의 config.pbtxt
name: "kcbert_onnx_ensemble"
platform: "ensemble"
max_batch_size: 0
 
input [
    {
        name: "input_text"
        data_type: TYPE_STRING
        dims: [-1, -1]  # 수정
    }
]
```

이제 Triton Inference Server를 재실행하고 성능 측정을 하러 갑니다.

```bash
$ docker run -it --ipc=host --net=host nvcr.io/nvidia/tritonserver:23.05-py3-sdk /bin/bash
...

root@server:/workspace# ./install/bin/perf_analyzer -m kcbert_onnx_ensemble -u 127.0.0.1:8000 --string-data="hello world" --shape=input_text:1,300
*** Measurement Settings ***
  Batch size: 1
  Service Kind: Triton
  Using "time_windows" mode for stabilization
  Measurement window: 5000 msec
  Using synchronous calls for inference
  Stabilizing using average latency
 
Request concurrency: 1
  Client:
    Request count: 1407
    Throughput: 78.1558 infer/sec
    Avg latency: 12787 usec (standard deviation 1695 usec)
    p50 latency: 12530 usec
    p90 latency: 13127 usec
    p95 latency: 13572 usec
    p99 latency: 17839 usec
    Avg HTTP time: 12780 usec (send/recv 58 usec + response wait 12722 usec)
  Server:
    Inference count: 1407
    Execution count: 1407
    Successful request count: 1407
    Avg request latency: 12423 usec (overhead 79 usec + queue 134 usec + compute 12210 usec)
 
  Composing models:
  kcbert_onnx, version: 1
      Inference count: 1407
      Execution count: 1407
      Successful request count: 1407
      Avg request latency: 11339 usec (overhead 65 usec + queue 96 usec + compute input 42 usec + compute infer 11103 usec + compute output 32 usec)
 
  onnx_tokenizer, version: 1
      Inference count: 1408
      Execution count: 1408
      Successful request count: 1408
      Avg request latency: 1091 usec (overhead 21 usec + queue 38 usec + compute input 25 usec + compute infer 963 usec + compute output 44 usec)
 
Inferences/Second vs. Client Average Batch Latency
Concurrency: 1, throughput: 78.1558 infer/sec, latency 12787 usec


root@server:/workspace# ./install/bin/perf_analyzer -m kcbert_trt_fp16_ensemble -u 127.0.0.1:8000 --string-data="hello world" --shape=input_text:1,300
*** Measurement Settings ***
  Batch size: 1
  Service Kind: Triton
  Using "time_windows" mode for stabilization
  Measurement window: 5000 msec
  Using synchronous calls for inference
  Stabilizing using average latency
 
Request concurrency: 1
  Client:
    Request count: 3447
    Throughput: 191.474 infer/sec
    Avg latency: 5221 usec (standard deviation 1260 usec)
    p50 latency: 5070 usec
    p90 latency: 5371 usec
    p95 latency: 5565 usec
    p99 latency: 8419 usec
    Avg HTTP time: 5216 usec (send/recv 51 usec + response wait 5165 usec)
  Server:
    Inference count: 3447
    Execution count: 3447
    Successful request count: 3447
    Avg request latency: 4928 usec (overhead 79 usec + queue 89 usec + compute 4760 usec)
 
  Composing models:
  kcbert_trt_fp16, version: 1
      Inference count: 3447
      Execution count: 3447
      Successful request count: 3447
      Avg request latency: 4062 usec (overhead 69 usec + queue 59 usec + compute input 55 usec + compute infer 3659 usec + compute output 220 usec)
 
  trt_tokenizer, version: 1
      Inference count: 3448
      Execution count: 3448
      Successful request count: 3448
      Avg request latency: 874 usec (overhead 18 usec + queue 30 usec + compute input 19 usec + compute infer 768 usec + compute output 38 usec)
 
Inferences/Second vs. Client Average Batch Latency
Concurrency: 1, throughput: 191.474 infer/sec, latency 5221 usec
```

이렇게하면 ONNX와 TensorRT 각 모델의 처리량(Throughput)과 평균 latency까지 확인할 수 있습니다.

`--concurrency-range 1:3` 옵션을 추가하면 request를 동시에 1개부터 3개까지 보낼 때의 성능을 측정할수도 있습니다.


## Reference
- <https://github.com/triton-inference-server/server/issues/4026>
- <https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/getting_started/quickstart.html>
- <https://github.com/triton-inference-server/client/blob/main/src/c++/perf_analyzer/README.md>
