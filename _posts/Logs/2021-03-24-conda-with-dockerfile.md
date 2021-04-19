---
title: Conda with Dockerfile
---

## install conda in Dockerfile

### TL;DR

- use `/opt/conda/bin/conda` for `conda` command is not added to the `PATH` yet for `root` user, which `Dockerfile` is using
- `~/miniconda.sh -b [-f] -p <prefix_path>` for installing conda `base` environment to a specific folder
- use `conda` command if <prefix_path> is set to `/usr/local`

**Steps**

- download `~/miniconda.sh` and install
  ```
  Installs Miniconda3 py38_4.9.2
  
  -b           run install in batch mode (without manual intervention),
               it is expected the license terms are agreed upon
  -f           no error if install prefix already exists
  -h           print this help message and exit
  -p PREFIX    install prefix, defaults to $PREFIX, must not contain spaces.
  -s           skip running pre/post-link/install scripts
  -u           update an existing installation
  -t           run package tests after installation (may install conda-build)
  ```

- update conda (optional): `conda update conda`

- clean conda by `conda clean --yes/-y --all/-a`

- optional clean: 

  - `rm -rf /var/lib/apt/lists/* /var/log/dpkg.log` `
  - `apt-get -qq -y remove curl bzip2`
  - `apt-get -qq -y autoremove`
  - `apt-get autoclean`  

### Examples

https://ngc.nvidia.com/catalog/containers/nvidia:pytorch/tags 19.05

- os: Ubuntu 16.04
- main packages:
  - conda
  - CUDA=10.1
  - pytorch=1.6
- conda installation path: `/opt/conda`
- my current `NER_Error_Recovery` project uses this image

```
docker pull nvcr.io/nvidia/pytorch:19.05-py3
docker history nvcr.io/nvidia/pytorch:19.05-py3 --no-trunc | less
# search conda
```

```
PYVER=3.6 /bin/sh -c curl -o ~/miniconda.sh -O https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh && \
chmod +x ~/miniconda.sh && \
~/miniconda.sh -b -p /opt/conda && \
rm ~/miniconda.sh && \
    /opt/conda/bin/conda install -y python=$PYVER conda-build numpy pyyaml scipy ipython mkl=2019.1 mkl-include=2019.1 spacy mock &&      /opt/conda/bin/conda clean -ya
```

[conda-docker (debian)](https://github.com/conda/conda-docker/blob/master/miniconda3/debian/Dockerfile)

- os: Debian
- main packages:
  - conda
- conda installation path: `/usr/local`

```
apt-get -qq update && apt-get -qq -y install curl bzip2 \
    && curl -sSL https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh -o /tmp/miniconda.sh \
    && bash /tmp/miniconda.sh -bfp /usr/local \
    && rm -rf /tmp/miniconda.sh \
    && conda install -y python=3 \
    && conda update conda \
    && apt-get -qq -y remove curl bzip2 \
    && apt-get -qq -y autoremove \
    && apt-get autoclean \
    && rm -rf /var/lib/apt/lists/* /var/log/dpkg.log \
    && conda clean --all --yes
```

