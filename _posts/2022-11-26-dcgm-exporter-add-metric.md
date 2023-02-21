---
title: DCGM-Exporter metric 항목 추가하기
toc: true
categories:
  - Kubernetes
tags:
  - DCGM-Exporter
  - DCGM_FI_DEV_FB_TOTAL
  - DCGM_FI_DEV_GPU_UTIL
  - dcp-metrics-included.csv
  - metric
---

**DCGM-Exporter**에서 기본적으로 제공되는 metric으로는 수집할 수 없는 항목들이 있습니다. (버전마다 metric 수집 항목의 차이가 있습니다.) 저는 2.4.0 버전을 사용중인데, `DCGM_FI_DEV_GPU_UTIL`(GPU utilization(%))과 `DCGM_FI_DEV_FB_TOTAL`(Total Memory(MB))가 필요했습니다.

<br>

## **수집 metric 수정**

metric을 수정하기 위해서는 **DCGM-Exporter** GitHub에서 `dcp-metrics-included.csv` 파일을 받아서 수정해야합니다. 사용중인 버전을 확인해서 Tag에 사용중인 버전을 찾아서  `/etc/dcp-metrics-included.csv`를 받고 필요한 metric이 이미 있으면 주석을 풀고 없으면 적당한 위치에 추가해줍니다.

`DCGM_FI_DEV_GPU_UTIL`과 `DCGM_FI_DEV_FB_TOTAL`을 추가한 예시

```yaml
## dcp-metrics-included.csv

# Utilization (the sample period varies depending on the product),,
DCGM_FI_DEV_GPU_UTIL,      gauge, GPU utilization (in %).
...

# Memory usage,,
DCGM_FI_DEV_FB_FREE, gauge, Framebuffer memory free (in MiB).
DCGM_FI_DEV_FB_USED, gauge, Framebuffer memory used (in MiB).
DCGM_FI_DEV_FB_TOTAL, gauge, Framebuffer memory total (in MB).
...
```

## **DCGM-Exporter Build**

이제 수정한 metric을 반영하기 위해서 image를 새로 빌드하겠습니다.

우선 수정한 `dcp-metrics-included.csv`를 기존 이미지에 덮어쓰는 내용의 **Dockerfile**을 만듭니다.

```dockerfile
# Dockerfile

FROM nvcr.io/nvidia/k8s/dcgm-exporter:2.2.9-2.4.0-ubuntu18.04
COPY dcp-metrics-included.csv /etc/dcgm-exporter/dcp-metrics-included.csv
```

그리고 image를 빌드합니다. 만약 Harbor와 같은 Image Registry를 사용중이라면 push까지 해줍니다.

```bash
$ docker build -t HARBOR_URL/project_name/nvcr.io/nvidia/k8s/dcgm-exporter:2.2.9-2.4.0-ubuntu18.04-am1 .
$ docker push HARBOR_URL/project_name/nvcr.io/nvidia/k8s/dcgm-exporter:2.2.9-2.4.0-ubuntu18.04-am1
```

<br>

이제 **Helm**으로 **DCGM-Exporter**를 설치합니다. 여기서 최신 버전이 받아져 꼬이지 않도록 버전을 명시해줬습니다.

```bash
$ helm repo add gpu-helm-charts https://nvidia.github.io/dcgm-exporter/helm-charts
$ helm repo update
$ helm pull gpu-helm-charts/dcgm-exporter --version 2.4.0 --untar
$ vi dcgm-exporter/values.yaml
image:
  repository: HARBOR_URL/project_name/nvcr.io/nvidia/k8s/dcgm-exporter
  pullPolicy: IfNotPresent
  # Image tag defaults to AppVersion, but you can use the tag key
  # for the image tag, e.g:
  tag: 2.2.9-2.4.0-ubuntu18.04-am1

$ helm install --generate-name -n prometheus gpu-helm-charts/dcgm-exporter --version 2.4.0 -f dcgm-exporter/values.yaml
```

**DCGM-Exporter** 설치가 완료됐으면 DCGM-Exporter Service를 포트포워딩해서 `/metrics`에 해당 metric이 잘 적용되었는지 확인합니다.

<br>

## **Reference**

- <https://github.com/NVIDIA/dcgm-exporter>
- <https://github.com/NVIDIA/gpu-monitoring-tools>
