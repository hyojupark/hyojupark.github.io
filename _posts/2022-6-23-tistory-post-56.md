---
title: CUDA, cuDNN 버전 확인
toc: true
categories:
  - Deep Learning, AI
tags:
  - CUDA
  - CuDNN
  - nvcc
  - nvidia-smi
  - Version
---

## **CUDA 버전 확인**

### **방법 1.**

```bash
$ nvidia-smi
```

### **방법 2.**

```bash
$ nvcc --version
```
<br>

## **cuDNN 버전 확인**

### **Windows.**

아래 경로에서 `CUDNN_MAJOR`, `CUDNN_MINOR`, `CUDNN_PATCHLEVEL` 값 확인 (v11.1 부분을 CUDA 버전에 맞춰 변경)
`C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v11.1\include\cudnn.h`

ex) cnDUNN 8.0.5
```c
...
#define CUDNN_MAJOR 8
#define CUDNN_MINOR 0
#define CUDNN_PATCHLEVEL 5
...
```

### **Linux.**

```bash
$ cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2
#define CUDNN_MAJOR 8
#define CUDNN_MINOR 0
#define CUDNN_PATCHLEVEL 5
```
