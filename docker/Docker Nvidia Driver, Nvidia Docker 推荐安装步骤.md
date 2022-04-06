> 操作系统：Ubuntu 16.04/18.04

## 安装 Nvidia Driver

推荐使用 graphics drivers PPA 安装 Nvidia 驱动。

```
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt update
```

检测推荐的 Nvidia 显卡驱动：

```
ubuntu-drivers devices
```

安装 Nvidia 驱动（以下是 RTX2060 上的情况）：

```
# Ubuntu 16.04 only search 430 for CUDA < 10.2
apt-cache search nvidia
sudo apt install nvidia-430

# Ubuntu 18.04 could search 440 for CUDA <= 10.2
apt-cache search nvidia | grep ^nvidia-driver
sudo apt install nvidia-driver-440
```

> 驱动对应的 CUDA 版本，请见 CUDA Compatibility: https://docs.nvidia.com/deploy/cuda-compatibility/index.html 。

最后， `sudo reboot` 重启。之后，运行 `nvidia-smi` 输出 Nvidia 驱动信息:

```
$ nvidia-smi
Fri Apr 17 07:31:55 2020
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 440.82       Driver Version: 440.82       CUDA Version: 10.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  GeForce RTX 2060    Off  | 00000000:01:00.0 Off |                  N/A |
| N/A   40C    P8     5W /  N/A |    263MiB /  5934MiB |      3%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|    0      1560      G   /usr/lib/xorg/Xorg                           144MiB |
|    0      1726      G   /usr/bin/gnome-shell                          76MiB |
|    0      2063      G   ...uest-channel-token=10544833948196615517    39MiB |
+-----------------------------------------------------------------------------+
```

> 如果安装 CUDA Toolkit ，请先了解 CUDA Compatibility: https://docs.nvidia.com/deploy/cuda-compatibility/index.html 。安装 CUDA Toolkit 时，注意其携带的驱动版本，最好将其与驱动分别进行安装。而驱动从官方上直接找合适的版本。

## 安装 Docker

```
# update the apt package index
sudo apt-get update
# install packages to allow apt to use a repository over HTTPS
sudo apt-get install apt-transport-https ca-certificates curl gnupg2 software-properties-common

# add Docker’s official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

# set up the stable repository
sudo add-apt-repository \
  "deb [arch=amd64] https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/ubuntu \
  $(lsb_release -cs) \
  stable"

# update the apt package index
sudo apt-get update
# install the latest version of Docker Engine and containerd
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

之后，将 Docker 设为 non-root 用户可用：

```
sudo groupadd docker
sudo usermod -aG docker $USER
```

### 参考

- Install Docker Engine on Ubuntu
  https://docs.docker.com/engine/install/ubuntu/
- Docker CE 清华源
  https://mirrors.tuna.tsinghua.edu.cn/help/docker-ce/
- Post-installation steps for Linux
  https://docs.docker.com/engine/install/linux-postinstall/

## 安装 Nvidia Docker

```
# add the package repositories
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list

sudo apt-get update
sudo apt-get install -y nvidia-container-toolkit
sudo systemctl restart docker
```

### 使用

```
#### Test nvidia-smi with the latest official CUDA image
docker run --gpus all nvidia/cuda:10.0-base nvidia-smi

# Start a GPU enabled container on two GPUs
docker run --gpus 2 nvidia/cuda:10.0-base nvidia-smi

# Starting a GPU enabled container on specific GPUs
docker run --gpus '"device=1,2"' nvidia/cuda:10.0-base nvidia-smi
docker run --gpus '"device=UUID-ABCDEF,1"' nvidia/cuda:10.0-base nvidia-smi

# Specifying a capability (graphics, compute, ...) for my container
# Note this is rarely if ever used this way
docker run --gpus all,capabilities=utility nvidia/cuda:10.0-base nvidia-smi
$ docker run --gpus all nvidia/cuda:10.2-base-ubuntu16.04 nvidia-smi
Unable to find image 'nvidia/cuda:10.2-base-ubuntu16.04' locally
10.2-base-ubuntu16.04: Pulling from nvidia/cuda
976a760c94fc: Pull complete
c58992f3c37b: Pull complete
0ca0e5e7f12e: Pull complete
f2a274cc00ca: Pull complete
708a53113e13: Pull complete
7dde2dc03189: Pull complete
2d21d4aba891: Pull complete
Digest: sha256:1423b386bb4f950d12b3b0f3ad51eba42d754ee73f8fc4a60657a1904993b68c
Status: Downloaded newer image for nvidia/cuda:10.2-base-ubuntu16.04
Fri Apr 24 08:17:26 2020
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 440.82       Driver Version: 440.82       CUDA Version: 10.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  GeForce RTX 2060    Off  | 00000000:01:00.0 Off |                  N/A |
| N/A   38C    P8    10W /  N/A |    523MiB /  5934MiB |     21%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
+-----------------------------------------------------------------------------+
```

### 参考

- nvidia-docker: https://github.com/NVIDIA/nvidia-docker
- nvidia/cuda: https://hub.docker.com/r/nvidia/cuda

## 结语