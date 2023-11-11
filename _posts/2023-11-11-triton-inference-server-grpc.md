---
title: Triton Inference Server에서 gRPC 사용하기
categories:
  - Triton
tags:
  - Triton
# published: false
---

## 1. tritonclient의 InferenceServerClient를 사용
```bash
$ pip install tritonclient[grpc]
```

```python
# main.py
import numpy as np
import tritonclient.grpc as trtgrpc
 
 
triton_client = trtgrpc.InferenceServerClient(url='127.0.0.1:8001')
 
inputs = []
inputs.append(trtgrpc.InferInput('input_text', [1], 'BYTES'))
inputs[0].set_data_from_numpy(np.array(['hello world!'], dtype=np.object_))
 
outputs = []
outputs.append(trtgrpc.InferRequestedOutput('label_index'))
outputs.append(trtgrpc.InferRequestedOutput('softmax'))
 
response = triton_client.infer(model_name='kcbert_trt_fp16_ensemble',
                               inputs=inputs,
                               outputs=outputs).get_response()
 
print(response)
 
label_index = np.frombuffer(response.raw_output_contents[0], dtype=np.int32)
label_index = np.resize(label_index, response.outputs[0].shape)
softmax = np.frombuffer(response.raw_output_contents[1], dtype=np.float32)
softmax = np.resize(softmax, response.outputs[1].shape)
 
print(label_index)
print(softmax)
```

```js
model_name: "kcbert_trt_fp16_ensemble"
model_version: "1"
id: "kcbert_trt_fp16_ensemble-id-0"
parameters {
  key: "sequence_end"
  value {
    bool_param: false
  }
}
parameters {
  key: "sequence_id"
  value {
    int64_param: 0
  }
}
parameters {
  key: "sequence_start"
  value {
    bool_param: false
  }
}
outputs {
  name: "label_index"
  datatype: "INT32"
  shape: 1
}
outputs {
  name: "softmax"
  datatype: "FP32"
  shape: 3
}
raw_output_contents: "\001\000\000\000"
raw_output_contents: "ad\361:\322EO?\357\005A>"
 
{'label_index': array([0], dtype=int32), 'softmax': array([9.9868244e-01, 1.1773579e-03, 1.4020398e-04], dtype=float32)}
```

## 2. tritonclient 없이 grpc만 사용

tritonclient.grpc의 pb 파일들(`service_pb2`, `service_pb2_grpc`, `model_config_pb2`)은 tritonclient와 dependency가 없기 때문에 복사해서 가져다 사용이 가능합니다.

grpc만 사용하면 grpcio만 설치하기 때문에 약 26MB 정도의 불필요한 추가 패키지를 설치하지 않아도 됩니다. 최대한 최적화해야할 때만 추천합니다.

```bash
$ pip install grpcio
```

```python
import grpc
import numpy as np
# from tritonclient.grpc import service_pb2, service_pb2_grpc, model_config_pb2
import service_pb2, service_pb2_grpc, model_config_pb2

 
with grpc.insecure_channel('localhost:8001') as channel:
    model_name = 'kcbert_trt_fp16_ensemble'
 
    stub = service_pb2_grpc.GRPCInferenceServiceStub(channel)
    request = service_pb2.ModelInferRequest()
    request.model_name = model_name
    request.model_version = ''
    request.id = model_name + '-id-0'
 
    model_input = service_pb2.ModelInferRequest().InferInputTensor()
    model_input.name = 'input_text'
    model_input.datatype = 'BYTES'
    model_input.shape.extend([2, 1])
    model_input.contents.bytes_contents.extend([bytes('hello', 'utf-8'), bytes('world', 'utf-8')])
 
    request.inputs.extend([model_input])
 
    output_label_index = service_pb2.ModelInferRequest().InferRequestedOutputTensor()
    output_label_index.name = 'label_index'
    output_softmax = service_pb2.ModelInferRequest().InferRequestedOutputTensor()
    output_softmax.name = 'softmax'
 
    request.outputs.extend([output_label_index, output_softmax])
 
    response = stub.ModelInfer(request)
 
label_index = np.frombuffer(response.raw_output_contents[0], dtype=np.int32)
label_index = np.resize(label_index, response.outputs[0].shape)
softmax = np.frombuffer(response.raw_output_contents[1], dtype=np.float32)
softmax = np.resize(softmax, response.outputs[1].shape)
```
