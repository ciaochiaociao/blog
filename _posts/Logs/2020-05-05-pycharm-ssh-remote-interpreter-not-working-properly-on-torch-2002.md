---
title: PyCharm SSH remote interpreter not working properly on TWCC Torch 20.02
---

PyCharm SSH remote interpreter  is not working properly on TWCC Torch 20.02  but works in TWCC Torch 19.11

environment:
 - SSH python remote interpreter in PyCharm
 - conda: 4.7.12
 - python: 3.6.9
 - PyCharm: 3.4
 - torch: 20.02

works in the python console activated from command line terminal but not work in PyCharm Python Console/Run
```
import torch
print(torch.cuda.is_available())
print(torch.cuda.device_count())
# False in PyCharm Python Console/Run
# True in Python console activated from command line terminal
```

My Solution:

- use pytorch 19.11 version
- update the environment variables with 

```
# export CREATOR_ACCOUNT=cwhsutw1
#export BAZELRC=/home/cwhsutw1/.bazelrc
#export HOME=/home/cwhsutw1
#export LOGNAME=cwhsutw1
#export MAIL=/var/mail/cwhsutw1
#export PWD=/home/cwhsutw1
#export USER=cwhsutw1
# export HOSTNAME=ammkmuctr1596693104528-ttsng
# export SHELL=/bin/bash
# export SHLVL=1

export ENV=/etc/shinit_v2

export LANG=C.UTF-8
export LC_ALL=C.UTF-8

export BASH_ENV=/etc/bash.bashrc

export COCOAPI_VERSION=2.0+nv0.4.0

export CUBLAS_VERSION=11.1.0.213
export CUDA_CACHE_DISABLE=1
export CUDA_DRIVER_VERSION=450.36.06
export CUDA_VERSION=11.0.167
export CUDNN_VERSION=8.0.1.13
export CUFFT_VERSION=10.1.3.191
export CURAND_VERSION=10.2.0.191
export CUSOLVER_VERSION=10.4.0.191
export CUSPARSE_VERSION=11.0.0.191
export _CUDA_COMPAT_PATH=/usr/local/cuda/compat
export TORCH_CUDA_ARCH_LIST="5.2 6.0 6.1 7.0 7.5 8.0+PTX"

export LD_LIBRARY_PATH=/usr/local/cuda/compat/lib:/usr/local/nvidia/lib:/usr/local/nvidia/lib64
export LIBRARY_PATH=/usr/local/cuda/lib64/stubs:

export NVIDIA_BUILD_ID=13419386
export NVIDIA_DRIVER_CAPABILITIES=compute,utility,video
export NVIDIA_PYTORCH_VERSION=20.06
export NVIDIA_REQUIRE_CUDA=cuda>=9.0
export NVIDIA_VISIBLE_DEVICES=GPU-8f27173b-c643-7862-7bb0-03ea94884832
export NVJPEG_VERSION=11.0.0.191
export NVM_BIN=/usr/local/nvm/versions/node/v14.4.0/bin
export NVM_CD_FLAGS=
export NVM_DIR=/usr/local/nvm
export NVM_INC=/usr/local/nvm/versions/node/v14.4.0/include/node

export PYTORCH_BUILD_NUMBER=0
export PYTORCH_BUILD_VERSION=1.6.0a0+9907a3e
export PYTORCH_VERSION=1.6.0a0+9907a3e

export PYTHONIOENCODING=utf-8
export PYTHONUNBUFFERED=1

export PATH=/opt/conda/bin:/opt/cmake-3.14.6-Linux-x86_64/bin/:/usr/local/mpi/bin:/usr/local/nvidia/bin:/usr/local/cuda/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/ucx/bin:/opt/tensorrt/bin #:/home/cwhsutw1/.local/bin:/home/cwhsutw1/.local/bin:/home/cwhsutw1/bin

export DALI_BUILD=1313465
export DALI_VERSION=0.22.0

export IBV_DRIVERS=/usr/lib/libibverbs/libmlx5

export JHUIB4CTR1596693104528_PORT=tcp://10.96.16.55:55176
export JHUIB4CTR1596693104528_PORT_55176_TCP=tcp://10.96.16.55:55176
export JHUIB4CTR1596693104528_PORT_55176_TCP_ADDR=10.96.16.55
export JHUIB4CTR1596693104528_PORT_55176_TCP_PORT=55176
export JHUIB4CTR1596693104528_PORT_55176_TCP_PROTO=tcp
export JHUIB4CTR1596693104528_PORT_56216_TCP=tcp://10.96.16.55:56216
export JHUIB4CTR1596693104528_PORT_56216_TCP_ADDR=10.96.16.55
export JHUIB4CTR1596693104528_PORT_56216_TCP_PORT=56216
export JHUIB4CTR1596693104528_PORT_56216_TCP_PROTO=tcp
export JHUIB4CTR1596693104528_SERVICE_HOST=10.96.16.55
export JHUIB4CTR1596693104528_SERVICE_PORT=55176
export JHUIB4CTR1596693104528_SERVICE_PORT_JCII3ECTR1596693104528=55176
export JHUIB4CTR1596693104528_SERVICE_PORT_MTW500CTR1596693104528=56216

export JUPYTER_PORT=8888

export KUBERNETES_PORT=tcp://10.96.0.1:443
export KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
export KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
export KUBERNETES_PORT_443_TCP_PORT=443
export KUBERNETES_PORT_443_TCP_PROTO=tcp
export KUBERNETES_SERVICE_HOST=10.96.0.1
export KUBERNETES_SERVICE_PORT=443
export KUBERNETES_SERVICE_PORT_HTTPS=443

#export SSH_CLIENT=10.244.146.128 55395 22
#export SSH_CONNECTION=10.244.146.128 55395 10.244.171.80 22
#export SSH_TTY=/dev/pts/2

export TENSORBOARD_PORT=6006

export TERM=xterm-256color

export MOFED_VERSION=4.6-1.0.1
export NCCL_VERSION=2.7.5
export NPP_VERSION=11.0.0.191
export NSIGHT_COMPUTE_VERSION=2020.1.0.33
export NSIGHT_SYSTEMS_VERSION=2020.2.5.8
export OPENMPI_VERSION=3.1.6
export OPENUCX_VERSION=1.6.1
export TRT_VERSION=7.1.2.8

export _=/usr/bin/env
export _apihost=k8s-taichung-default
export _project_code=MST108218
export site_id=1116757

```

```
# convert export.sh to PyCharm variables list
awk -F ' ' 'BEGIN {ORS=";"} {print $2;}' <file_below>
```

