---
layout: article
title: ubuntu 18.04 nvidia driver, cuda, cudnn 설치
aside:
 toc: true
tags: linux
disqus: true
---

# ubuntu 18.04 nvidia, cuda, cudnn, docker-nvidia 설치

### 1. GPU 하드웨어와 호환되는 nvidia driver version 확인

nvidia에서 제공하는 gpu 관련 패키지 (ex. cuda, cudnn) 또는 tensorflow, torch를 설치하기 전에 nvidia 드라이버를 먼저 설치해야한다. 

내가 갖고 있는 gpu 모델은 **NVIDIA TITAN RTX** 이다.



[nvidia 하드웨어별 드라이버 버전 확인 사이트](https://www.nvidia.com/Download/Find.aspx?lang=en-us) 에서 나의 GPU 모델명을 입력하면 다음과 같은 결과가 나온다. NVIDIA Recommended인 **450.119.03**버전을 선택한다. 

<img src="/Users/ssj/Documents/ChristinaROK.github.io/assets/nvidia_driver_version.png" alt="Screen Shot 2021-07-09 at 1.01.44 AM" style="zoom: 67%;" />



Ubuntu의 패키지 관리 명령어인 `apt-get` 로 nvidia driver를 설치하려고 했다. 

```sh
$ sudo apt-get install --no-install-recommends nvidia-driver-450
$ sudo reboot # 시스템 재부팅을 해줘야 nvidia driver 설치가 완료된다.
```

패키지 설치를 완료한 후 nvidia driver의 버전을 확인했다. 

```sh
$ cat /proc/driver/nvidia/version
# NVRM version: NVIDIA UNIX x86_64 Kernel Module  470.42.01  Tue Jun 15 21:26:37 UTC 2021
# GCC version:  gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)
```

또는 

```sh
$ nvidia-smi
```

로 버전을 확인할 수 있다. 



그런데 롸??? 난 분명히 패키지의 버전을 450으로 설정했는데 확인해보니 470이 깔려있다. 



nvidia-driver 이름으로 시작하는 패키지를 검색해보자. `api-cache` 는 low level ubuntu 패키지 관리 명령어로 search 명령어와 사용해 패키지를 검색할 수 있다. 

```sh
$ apt-cache search nvidia-driver
# nvidia-driver-390 - NVIDIA driver metapackage
# ...
# nvidia-driver-450 - NVIDIA driver metapackage
# nvidia-driver-470 - NVIDIA driver metapackage
```

분명 450 패키지가 존재하는데 왜 470 패키지가 대신 깔린지 모르겠다 ㅠ 



일단 스킵. 따라서 현재 서버 스펙은 다음과 같다. 

> OS : Linux
>
> Distribution: Ubuntu 18.04
>
> Architecture: x86_64
>
> GPU Model : Nvidia Titan RTX
>
> Nvidia Driver Version : 470.47.01 (beta version)

---

### 2. tensorflow & torch와 호환되는 cuda, cudnn version 확인

tensorflow 버전별로 호환되는 python, gcc, cuDNN, CUDA 버전은 다음과 같다. 

<img src="/Users/ssj/Documents/ChristinaROK.github.io/assets/tensorflow_version.png" alt="Screen Shot 2021-07-09 at 1.37.12 AM" style="zoom:70%;" />

<img src="/Users/ssj/Documents/ChristinaROK.github.io/assets/pytorch_version.png" alt="Screen Shot 2021-07-09 at 1.58.49 AM" style="zoom:67%;" />

CUDA 버전은 torch 1.7.1, tensorflow 2.*를 사용한다는 가정하에 호환이 가능하면서 가장 최신 버전인 **11.0**을 선택했다. cuDNN 버전은 cuda와 호환되는  **8.0.4** 를 선택했다.



CUDA 다운로드는 [Nvidia Cuda Tookit Archive](https://developer.nvidia.com/cuda-11.0-download-archive?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1804&target_type=debnetwork)에서 나의 환경에 맞는 설치 방식을 정한다. 나의 경우 network, 즉 `apt-get` 명령어를 통해 패키지를 설치하는 방법을 택했다. 

```sh
# CUDA 패키지 (cuda-11-0) 설치
$ wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
$ sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
$ sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
$ sudo add-apt-repository "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/ /"

# cuDNN 패키지 (libcudnn) 설치 
$ wget http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64/nvidia-machine-learning-repo-ubuntu1804_1.0.0-1_amd64.deb
$ sudo apt install ./nvidia-machine-learning-repo-ubuntu1804_1.0.0-1_amd64.deb
sudo apt-get update

sudo apt-get install --no-install-recommends \
    cuda-11-0 \
    libcudnn8=8.0.4.30-1+cuda11.0  \
    libcudnn8-dev=8.0.4.30-1+cuda11.0
```

설치가 완료되면 환경 변수를 등록하는 일만 남았다. 

홈 디렉토리에 있는 `~/.profile` 파일을 열어서 서버가 부팅시 생성하는 환경 변수에 CUDA 설치 위치를 추가해주자. 

```sh
# ~/.profile
export PATH=$PATH:/usr/local/cuda-11.0/bin
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-11.0/lib64
```

파일을 저장한 후 

```sh
$ source ~/.profile
```

`source` 명령어로 변경된 사항을 서버에 반영하자. 



이제 설치된 CUDA 버전을 확인해보자.

```sh
$ nvcc --version 
# nvcc: NVIDIA (R) Cuda compiler driver
# Copyright (c) 2005-2020 NVIDIA Corporation
# Built on Wed_Jul_22_19:09:09_PDT_2020
# Cuda compilation tools, release 11.0, V11.0.221
# Build cuda_11.0_bu.TC445_37.28845127_0
```

설치된 cuDNN을 확인해보자. 

```sh
$ ldconfig
$ ldconfig -N -v $(sed 's/:/ /' <<< $LD_LIBRARY_PATH) 2>/dev/null | grep libcudnn
#	libcudnn_adv_train.so.8 -> libcudnn_adv_train.so.8.0.4
#	libcudnn_ops_infer.so.8 -> libcudnn_ops_infer.so.8.0.4
#	libcudnn_ops_train.so.8 -> libcudnn_ops_train.so.8.0.4
#	libcudnn_cnn_train.so.8 -> libcudnn_cnn_train.so.8.0.4
#	libcudnn.so.8 -> libcudnn.so.8.0.4
#	libcudnn_cnn_infer.so.8 -> libcudnn_cnn_infer.so.8.0.4
#	libcudnn_adv_infer.so.8 -> libcudnn_adv_infer.so.8.0.4
```

다음과 같이 결과가 나오면 설치가 완료된 것이다. 



결과적으로 다음과 같은 스펙이 완성됐다! 

>OS : Linux
>
>Distribution: Ubuntu 18.04
>
>Architecture: x86_64
>
>GPU Model : Nvidia Titan RTX
>
>Nvidia Driver Version : 470.47.01 (beta version)
>
>CUDA Version: 11.0
>
>cuDNN Version: 8.0.4

# Reference 

* Official Docs
  * [Tensorflow configuration](https://www.tensorflow.org/install/source#tested_build_configurations)
  * [Torch configuration](https://pytorch.org/get-started/previous-versions/)
  * [CUDA Toolkit Donwload](https://developer.nvidia.com/cuda-11.0-download-archive?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1804&target_type=debnetwork)
  * [tensorflow gpu how_to_install](https://www.tensorflow.org/install/gpu?hl=ko#older_versions_of_tensorflow)

* Nvidia Driver
  * [Linux에서 각종 nvidia 드라이버 패키지 (apt-get)으로 다운받기: nvidia APT repository ](https://hiseon.me/linux/ubuntu/nvidia-apt-repository/)
  * [linux apt 명령어 설명 ](https://ngee.tistory.com/128)
  * [linux api 명령어 사용법 정리](https://itsfoss.com/apt-command-guide/)
  * [linux package (apt) 설명](https://seulcode.tistory.com/548)
* 설치시 참고한 블로그 
  * [1](https://ghostweb.tistory.com/832)
  * [2](https://cafepurple.tistory.com/39)
* docker-nvidia
  * [nvidia container toolkit official](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#docker)
  * [nvidia docker install](https://kyumdoctor.tistory.com/22)

