---
title: PyTorch 전체 seed 고정 (Reproducibility)
toc: true
categories:
  - Deep Learning, AI
tags:
  - CUDA
  - CuDNN
  - Deterministic
  - manual_seed
  - pytorch
  - random
  - reproducibility
  - SEED
---

딥러닝 성능을 올리기 위해서는 성능 재현 및 실험이 필수입니다. PyTorch는 다른 프레임워크에 비해 비교적 쉽게 seed 고정이 가능했습니다.

```python
seed = 42

# step 1
os.environ['PYTHONHASHSEED'] = str(seed)
random.seed(seed)
np.random.seed(seed)
torch.manual_seed(seed)
torch.cuda.manual_seed_all(seed)

# step 2
torch.use_deterministic_algorithms(True)
torch.backends.cudnn.benchmark = False
```

**step 1**은 일반적인 seed 고정 과정으로, 특수한 경우가 아니라면 학습전에 꼭 실행하는게 좋습니다. 

**step 2**는 고정하지않으면 점점 미세하게 차이가 발생할 수 있는데, 고정하면 학습 속도가 느려질 수 있으므로 감안하고 사용해야합니다.
