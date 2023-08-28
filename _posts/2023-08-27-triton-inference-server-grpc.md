---
title: Triton Inference Server에서 gRPC 사용하기
categories:
  - Triton
tags:
  - Triton
published: false
---

```bash
$ pip install tritonclient[grpc]
```

```python
# main.py
import grpc
import numpy as np
from tritonclient.grpc import service_pb2, service_pb2_grpc, model_config_pb2
 
 
with grpc.insecure  _channel('localhost:8001') as channel:
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

