---
title: Cuda / CuDNN
---

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

    

