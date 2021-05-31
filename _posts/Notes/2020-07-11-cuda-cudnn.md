---
title: Cuda / CuDNN
---

## Commands

```bash
# Check Cuda/cudnn in pytorch
In [2]: torch.cuda.current_device()
Out[2]: 0

In [3]: torch.cuda.device(0)
Out[3]: <torch.cuda.device at 0x7efce0b03be0>

In [4]: torch.cuda.device_count()
Out[4]: 1

In [5]: torch.cuda.get_device_name(0)
Out[5]: 'GeForce GTX 950M'

In [6]: torch.cuda.is_available()
Out[6]: True

# check cuda version
## in terminal
nvcc -V
cat /usr/local/cuda/version.txt
## in jupyter
torch.version.cuda

# install cudnn
#download tgz or deb file from nvidia with login account
tar -xzvf cudnn-9.0-linux-x64-v7.tgz
sudo cp cuda/include/cudnn.h /usr/local/cuda/include
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*

#or you can install from conda where when you try to install pytorch, it will also install cudnn for you.
conda install pytorch cudatoolkit=10.0

# get cudnn version
cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2

# set LD_LIBRARY_PATH (CUDA 10.0 as an example)
LD_LIBRARY_PATH: the search path environment variable for the linux shared library
export LD_LIBRARY_PATH="/usr/local/nvidia/lib:/usr/local/nvidia/lib64:/usr/local/cuda-10.0/lib64:/usr/local/cuda-10.0/extras/CUPTI/lib64"

# Use CUDA 9.0 in CUDA 10.0 environment
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-9.0/lib64
export PATH=$PATH:/usr/local/cuda-9.0/bin
export CUDA_HOME=$CUDA_HOME:/usr/local/cuda-9.0
```

## NLU Equipment

1. NVIDIA-SMI display/driver version: 415.18
2. CUDA Version: 10.0.130
3. CuDNN Version: 7.6.2
4. Pytorch: 1.2.0
5. GPU: Nvidia GeForce RTX 2080Ti

CUDA Toolkit and Compatible Driver

https://docs.nvidia.com/deploy/cuda-compatibility/index.html

## Issues: torch.cuda.is_available() returns False

- Cause: Docker base image of `dnnnlp:v2.1` is used for `ciaochiaociao/recovery:0.1`. But at some point, I decided to reduce the image size by using `docker export` & `docker import` to get rid of all intermediate layers. This causes the problem because all environment variables are lost. Especially those defined in creating the first image by `Dockerfile`, which are fairly important. (see https://hub.docker.com/layers/nvidia/cuda/latest/images/sha256-3f5d21af43027e9e883e15e9019ca59cb8a2f1965b82b22de3252ed05f0c1af9?context=explore)

- What I have done before: nvidia-smi did see GPU

- Solutions:

  - Use the flag `-e` of `docker run` to set back those environment variables, listed as below:

    ```
    PATH=/usr/local/nvidia/bin:/usr/local/cuda/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
    CUDA_VERSION=10.0.130
    CUDA_PKG_VERSION=10-0=10.0.130-1
    LD_LIBRARY_PATH=/usr/local/nvidia/lib:/usr/local/nvidia/lib64
    NVIDIA_DRIVER_CAPABILITIES=compute,utility
    NVIDIA_REQUIRE_CUDA=cuda>=10.0 brand=tesla,driver>=384,driver<385 brand=tesla,driver>=410,driver<411
    NCCL_VERSION=2.4.2
    LIBRARY_PATH=/usr/local/cuda/lib64/stubs
    LANG=C.UTF-8
    LC_ALL=C.UTF-8
    ```

    

