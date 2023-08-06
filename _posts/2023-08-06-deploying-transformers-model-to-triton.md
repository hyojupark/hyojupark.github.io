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
published: false
---

일반적인 PyTorch 혹은 Transformers 모델 배포 성능을 극대화하기 위해 모델 포맷을 **ONNX**, **TensorRT**로 변환하고 **Triton Inference Server**로 배포하는 과정을 정리해봤습니다.

<br>

순서는 아래와 같습니다.
  1. 모델 학습
  2. ONNX 포맷 변환
  3. TensorRT 포맷 변환
  4. 배포 모델 구조 정의
  5. Trtion Inference Server 시작
  6. 테스트


## 1. 모델 학습

우선 Transformers 모델을 학습시키겠습니다. BERT 기반 중 하나인 `kcbert-large`([GitHub](https://github.com/Beomi/KcBERT))를 사용해서 감정분류(긍정, 중립, 부정) 모델을 학습시킵니다. 학습 코드는 짜로 작성하지 않았는데, 적당한 감정분석 데이터셋(ex. [금융 뉴스 문장 감성 분석 데이터셋](https://github.com/ukairia777/finance_sentiment_corpus))을 찾아서 하시면 됩니다.

학습에는 `pytorch/pytorch:1.11.0-cuda11.3-cudnn8-devel` 이미지에 `transformers 4.18.0` 패키지를 설치해서 사용했습니다. 

```python
from transformers import AutoTokenizer, BertForSequenceClassification
import torch 
 
 
tokenizer = AutoTokenizer.from_pretrained("beomi/kcbert-large")
model = BertForSequenceClassification.from_pretrained("beomi/kcbert-large", num_labels=3).to('cuda')
 
...
 
tokenizer.save_pretrained('tokenizer_model')
torch.save(model.state_dict(), 'model.pt')
```

학습한 모델을 저장할 때는 사용한 Tokenizer와 모델을 따로 저장했는데, Tokenizer는 뒤에 따로 사용할 예정이고 모델은 ONNX, TensorRT로 변환하기 쉽도록 PyTorch 모델로 저장할 때 `state_dict`를 함께 저장합니다.

<br>

## 2. ONNX 포맷 변환

저장한 PyTorch 모델(`model.pt`)을 ONNX 모델 포맷으로 변환합니다. 여기서 중요한 부분은 학습할 때 사용한 Tokenizer의 `max_length` 값을 input tensor의 length로 넣어주면 됩니다.

아래 코드는 위 학습에 사용된 이미지에서 실행하면 됩니다.

```python
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

여기서는 PyTorch 모델 파일을 ONNX 모델 파일로 변환하기 위한 코드를 작성했지만, 사용하실 때는 위 학습코드 밑에 붙여서 PyTorch 모델 파일 생성 과정을 생략해도 상관없습니다.

<br>

## 3. TensorRT 포맷 변환

ONNX 모델(`model.onnx`)을 TensorRT 모델 포맷으로 변환할 때는 TensorRT의 `OnnxParser`를 이용해서 변환합니다. 때문에 TensorRT 이미지를 하나 가져와서 아래 코드를 실행시켜주면 됩니다. ONNX 변환할 때와 동일하게 `max_length`를 수정하면 되며, 이미지는 (#TODO: TensorRT 이미지 버전)을 사용했습니다.

```python
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
│   │   ├── config.json
│   │   ├── model.py
│   │   ├── special_tokens_map.json
│   │   ├── tokenizer_config.json
│   │   ├── tokenizer.json
│   │   └── vocab.txt
│   └── config.pbtxt
└── trt_tokenizer
    ├── 1
    │   ├── config.json
    │   ├── model.py
    │   ├── special_tokens_map.json
    │   ├── tokenizer_config.json
    │   ├── tokenizer.json
    │   └── vocab.txt
    └── config.pbtxt
```

### 4.2 모델 설정

모델을 저장할 때는 모델 디렉토리 안에서 모델 버전 디렉토리에 모델 파일을 저장하고, 모델 설정 파일(`config.pbtxt`)를 저장합니다.




<br>

## 5. Triton Inference Server 시작



## 6. 테스트