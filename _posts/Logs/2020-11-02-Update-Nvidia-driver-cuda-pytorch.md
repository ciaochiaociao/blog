---
title: update nvidia driver, CUDA, and PyTorch on 51 server
---

## Information
 - https://www.cyberciti.biz/faq/ubuntu-linux-install-nvidia-driver-latest-proprietary-driver/
 - https://www.geeks3d.com/20111229/how-to-update-nvidia-graphics-drivers-under-linux/
 - (*) https://askubuntu.com/questions/61396/how-do-i-install-the-nvidia-drivers
 - (*) https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#pre-installation-actions
 - https://ngc.nvidia.com/catalog/containers/nvidia:pytorch
 - https://hub.docker.com/r/nvidia/cuda/tags


 - Ubuntu version: 16.04.5 LTS (xenial)

 - nividia driver
	 - Original: `415.18`
	 - Latest from `graphics-drivers/ppa`: `430.64` (2020-07-15) 
	 	(https://launchpad.net/~graphics-drivers/+archive/ubuntu/ppa/+packages)
	 - Latest and long-lived from `nvidia.com/Download`: `450.80.02` => no compiled source for **16.04** in `graphics-drivers/ppa`


## 1. Update nvidia driver
```
1. 
hwinfo --gfxcard --short
sudo lshw -C display

2. 
sudo apt update

2.1.
# unfound public key for nvidia.github.io
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | \
  sudo apt-key add -  # https://github.com/NVIDIA/nvidia-docker/issues/1081#issuecomment-533717708

sudo apt update

3.
apt search nvidia-4
OR apt-cache search nvidia-driver
egrep -v '^#|^ *$' /etc/apt/sources.list /etc/apt/sources.list.d/*

4. 
sudo add-apt-repository ppa:graphics-drivers/ppa
and repeat step 2 and 3

5.
sudo apt install nvidia-430

6.
sudo reboot

7. validate
cat /proc/driver/nvidia/version

```
## 2. Use docker to install from nvcr.io CUDA, PyTorch, Tensorflow automatically

- nvcr.io: 

  - includes perinstalled `CUDA Tookit` and `pytorch` or `tensorflow`
  - optmizes on each specific GPU

- installation: https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#docker

  ```
  distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
     && curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add - \
     && curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
  sudo apt-get update
  sudo apt-get install -y nvidia-docker2
  sudo systemctl restart docker
  sudo docker run --rm --gpus all nvidia/cuda:10.1-base nvidia-smi
  ```

- pull image from https://ngc.nvidia.com/catalog/containers/nvidia:pytorch/tags, https://ngc.nvidia.com/catalog/containers/nvidia:tensorflow/tags

  - version number: `<year_short>.<month>-py3`
  - check nvidia driver release date from google  (https://www.nvidia.com/Download/driverResults.aspx/147182/en-us -> 2019.5.9)
  ```
  docker pull nvcr.io/nvidia/pytorch:19.05-py3
  docker pull nvcr.io/nvidia/tensorflow:19.05-py3
  ```

## 2.1 Install CUDA Manually

 CUDA 10.1

```
2.0 Remove Old CUDA
sudo apt-get remove --purge cuda
sudo apt-get --purge remove nvidia-455

2.1. Verify You Have a CUDA-Capable GPU
lspci | grep -i nvidia

2.2. Verify You Have a Supported Version of Linux
uname -m && cat /etc/*release

2.3. Verify the System Has gcc Installed
gcc --version

2.4. Verify the System has the Correct Kernel Headers and Development Packages Installed
apt list | grep linux-headers-$(uname -r)
or install by
sudo apt-get install linux-headers-$(uname -r)

2.5. Choose an Installation Method
 - [] distribution-independent package
 - [x] distribution-specific package (recommended)

2.6. Download the NVIDIA CUDA Toolkit

rm /etc/apt/sources.list.d/cuda.list  # orig content of /etc/apt/sources.list.d/cuda.list: deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64 /

#16.04
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/cuda-repo-ubuntu1604_10.1.105-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu1604_10.1.105-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu1604_10.1.243-1_amd64.deb
sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub
sudo apt-get update
sudo apt-get install cuda-10-1  # do not run this if you need a specific version other than the later driver of nvidia (e.g. 455) for it will upgrade automatically
sudo apt install cuda-toolkit-10-1 cuda-libraries-10-1 cuda-license-10-1
sudo ln -s /usr/local/cuda-10.2/lib64/libcublas.so.10.2.2.214 libcublas.so.10
# cuda-10-1: 
#	1. cuda-runtime-10-1 -> cuda-drivers (--> nvidia-455), cuda-libraries-10-1, cuda-license-10-1
#	2. cuda-demo-suite-10-1 -> cuda-runtime-10-1
#	3. cuda-toolkit-10-1

#18.04
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-repo-ubuntu1804_10.1.105-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu1804_10.1.105-1_amd64.deb
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
sudo apt-get update
sudo apt-get install cuda

3. post-installation
#https://askubuntu.com/questions/866161/setting-path-variable-in-etc-environment-vs-profile

3.1 setting Path
sudo echo 'export PATH=/usr/local/cuda-10.1/bin${PATH:+:${PATH}}' > /etc/profile.d/set-cuda-env-var-by-cwhsu.sh

3.2 setting LD_LIBRARY_PATH
sudo echo 'export LD_LIBRARY_PATH=/usr/local/cuda-10.1/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}' >> /etc/profile.d/set-cuda-env-var-by-cwhsu.sh

3.3 validate
nvidia-smi
nvcc --version
cat /usr/local/cuda/version.txt

```

## 2.2 Install Pytorch Manually

```
4.1.1 Conda torch 1.6 on cuda 10.1
conda install pytorch==1.6.0 torchvision==0.7.0 cudatoolkit=10.1 -c pytorch

4.1.2 Conda latest torch (1.7.0) on cuda 10.1
conda install pytorch torchvision torchaudio cudatoolkit=10.1 -c pytorch

4.1.3 Pip torch 1.6 on cuda 10.1
pip install torch==1.6.0+cu101 torchvision==0.7.0+cu101 -f https://download.pytorch.org/whl/torch_stable.html

4.1.4 Pip latest torch (1.7.0) on cuda 10.1
pip install torch==1.7.0+cu101 torchvision==0.8.1+cu101 torchaudio==0.7.0 -f https://download.pytorch.org/whl/torch_stable.html
```

## 2.3 Install TensorFlow Manually

see: https://www.tensorflow.org/install/pip

```
$ sudo apt update
$ sudo apt install python3.X-dev python3-pip python3.7-venv

$ pip install --upgrade pip
(venv) $ pip install --upgrade tensorflow
$ pip3 install --user --upgrade tensorflow  # install in $HOME

# verify
$ python -c "import tensorflow as tf;print(tf.reduce_sum(tf.random.normal([1000, 1000])))"

```



## Results

### From Step 5.

```
 A new version (/tmp/grub.1glVeeNEMh) of configuration file /etc/default/grub is available, but the version installed currently has been locally modified.
 ┌───────────────────┤ Configuring grub-efi-amd64 ├───────────────────┐
 │                                                                    │
 │ Line by line differences between versions                          │
 │                                                                    │
 │ --- /etc/default/grub root.root 0644 2018-12-04 17:23:21           │
 │ +++ /tmp/grub.1glVeeNEMh root.root 0644 2020-11-02 12:07:24        │
 │ @@ -4,9 +4,9 @@                                                    │
 │  # info -f grub -n 'Simple configuration'                          │
 │                                                                    │
 │  GRUB_DEFAULT=0                                                    │
 │ -#GRUB_HIDDEN_TIMEOUT=0                                            │
 │ +GRUB_HIDDEN_TIMEOUT=0                                             │
 │  GRUB_HIDDEN_TIMEOUT_QUIET=true                                    │
 │ -GRUB_TIMEOUT=2                                                    │
 │ +GRUB_TIMEOUT=10                                                   │
 │  GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`  │
 │  GRUB_CMDLINE_LINUX_DEFAULT=""                                     │
 │  GRUB_CMDLINE_LINUX=""

```


### From step 4.

```
 Fresh drivers from upstream, currently shipping Nvidia.

## Current Status

Current long-lived branch release: `nvidia-430` (430.40)
Dropped support for Fermi series (https://nvidia.custhelp.com/app/answers/detail/a_id/4656)

Old long-lived branch release: `nvidia-390` (390.129)

For GF1xx GPUs use `nvidia-390` (390.129)
For G8x, G9x and GT2xx GPUs use `nvidia-340` (340.107)
For NV4x and G7x GPUs use `nvidia-304` (304.137) End-Of-Life!

Support timeframes for Unix legacy GPU releases:
https://nvidia.custhelp.com/app/answers/detail/a_id/3142

## What we're working on right now:

- Normal driver updates
- Help Wanted: Mesa Updates for Intel/AMD users, ping us if you want to help do this work, we're shorthanded.

## WARNINGS:

This PPA is currently in testing, you should be experienced with packaging before you dive in here:

Volunteers welcome!

### How you can help:

## Install PTS and benchmark your gear:

    sudo apt-get install phoronix-test-suite

Run the benchmark:

    phoronix-test-suite default-benchmark openarena xonotic tesseract gputest unigine-valley

and then say yes when it asks you to submit your results to openbechmarking.org. Then grab a cup of coffee, it takes a bit for the benchmarks to run. Depending on the version of Ubuntu you're using it might preferable for you to grabs PTS from upstream directly: http://www.phoronix-test-suite.com/?k=downloads

## Share your results with the community:

Post a link to your results (or any other feedback to): https://launchpad.net/~graphics-drivers-testers

Remember to rerun and resubmit the benchmarks after driver upgrades, this will allow us to gather a bunch of data on performance that we can share with everybody.

If you run into old documentation referring to other PPAs, you can help us by consolidating references to this PPA.

If someone wants to go ahead and start prototyping on `software-properties-gtk` on what the GUI should look like, please start hacking!

## Help us Help You!

We use the donation funds to get the developers hardware to test and upload these drivers, please consider donating to the "community" slider on the donation page if you're loving this PPA:

http://www.ubuntu.com/download/desktop/contribute
 More info: https://launchpad.net/~graphics-drivers/+archive/ubuntu/ppa
Press [ENTER] to continue or ctrl-c to cancel adding it

gpg: keyring `/tmp/tmpfha113zp/secring.gpg' created
gpg: keyring `/tmp/tmpfha113zp/pubring.gpg' created
gpg: requesting key 1118213C from hkp server keyserver.ubuntu.com
gpg: /tmp/tmpfha113zp/trustdb.gpg: trustdb created
gpg: key 1118213C: public key "Launchpad PPA for Graphics Drivers Team" imported
gpg: no ultimately trusted keys found
gpg: Total number processed: 1
gpg:               imported: 1  (RSA: 1)
sh: 0: getcwd() failed: No such file or directory
sh: 0: getcwd() failed: No such file or directory
sh: 0: getcwd() failed: No such file or directory
OK
```

### From Step 2.
```
Get:1 https://download.docker.com/linux/ubuntu xenial InRelease [66.2 kB]
Hit:2 http://us.archive.ubuntu.com/ubuntu xenial InRelease
Hit:3 http://security.ubuntu.com/ubuntu xenial-security InRelease
Hit:4 http://us.archive.ubuntu.com/ubuntu xenial-updates InRelease
Hit:6 http://us.archive.ubuntu.com/ubuntu xenial-backports InRelease
Err:5 https://nvidia.github.io/libnvidia-container/ubuntu16.04/amd64  InRelease
  The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 6ED91CA3AC1160CD
Err:7 https://nvidia.github.io/nvidia-container-runtime/ubuntu16.04/amd64  InRelease
  The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 6ED91CA3AC1160CD
Err:8 https://nvidia.github.io/nvidia-docker/ubuntu16.04/amd64  InRelease
  The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 6ED91CA3AC1160CD
Fetched 66.2 kB in 1s (53.0 kB/s)
Reading package lists... Done
Building dependency tree
Reading state information... Done
467 packages can be upgraded. Run 'apt list --upgradable' to see them.
W: An error occurred during the signature verification. The repository is not updated and the previous index files will be used. GPG error: https://nvidia.github.io/libnvidia-container/ubuntu16.04/amd64  InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 6ED91CA3AC1160CD
W: An error occurred during the signature verification. The repository is not updated and the previous index files will be used. GPG error: https://nvidia.github.io/nvidia-container-runtime/ubuntu16.04/amd64  InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 6ED91CA3AC1160CD
W: An error occurred during the signature verification. The repository is not updated and the previous index files will be used. GPG error: https://nvidia.github.io/nvidia-docker/ubuntu16.04/amd64  InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 6ED91CA3AC1160CD
W: Failed to fetch https://nvidia.github.io/libnvidia-container/ubuntu16.04/amd64/InRelease  The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 6ED91CA3AC1160CD
W: Failed to fetch https://nvidia.github.io/nvidia-container-runtime/ubuntu16.04/amd64/InRelease  The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 6ED91CA3AC1160CD
W: Failed to fetch https://nvidia.github.io/nvidia-docker/ubuntu16.04/amd64/InRelease  The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 6ED91CA3AC1160CD
W: Some index files failed to download. They have been ignored, or old ones used instead.
```