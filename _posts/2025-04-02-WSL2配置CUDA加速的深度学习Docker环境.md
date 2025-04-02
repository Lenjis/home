---
title: "WSL2配置CUDA加速的深度学习Docker环境"
tags: [WSL, CUDA, DL, Docker, CN]
style: fill # fill / border
color: secondary # primary / secondary / success / danger / warning / info / light / dark
description: 基于[Eric Moore的文章](https://eric-gitta-moore.github.io/2023/full-stack-development-and-deep-learning-tensorflow-pytorch-gpu-acceleration-using-nvidia-docker-in-wsl2/)和官方文档修改
---

> 基于[Eric Moore的文章](https://eric-gitta-moore.github.io/2023/full-stack-development-and-deep-learning-tensorflow-pytorch-gpu-acceleration-using-nvidia-docker-in-wsl2/)和官方文档修改

## （可选）迁移WSL2虚拟磁盘

```shell
wsl --shutdown
wsl --export Debian C:\WSL2\Debian\ext4.vhdx --vhd
wsl --unregister Debian
wsl --import-in-place Debian C:\WSL2\Debian\ext4.vhdx
```

## 安装Docker

安装依赖

```shell
apt update
apt install ca-certificates curl gnupg
```

下载并添加Docker的GPG公钥

```shell
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

添加docker源（使用NJU镜像）

```shell
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://mirror.nju.edu.cn/docker-ce/linux/debian \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

安装Docker

```shell
apt update
apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

## 安装NVIDIA Container Tookit

配置生产目录

```shell
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
  && curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
    sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
    sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
```

安装nvidia-container-toolkit（[nvidia-container-toolkit和nvidia-docker2的区别](https://github.com/NVIDIA/nvidia-docker/issues/1268#issuecomment-632692949)）

```shell
apt update
apt install nvidia-container-toolkit 
```

配置nvidia-ctk

```shell
nvidia-ctk runtime configure --runtime=docker
systemctl restart docker
```

## 附录：Docker和虚拟机的区别

![VM_vs_Docker](assets\images\VM_vs_Docker.png)

区分容器和基于 hypervisor 的虚拟机（ vm ）很重要。 vm 允许操作系统的多个副本，甚至多个不同的操作系统共享一台机器。每个虚拟机可以承载和运行多个应用程序。相比之下，容器被设计成虚拟化单个应用程序，并且部署在主机上的所有容器共享一个操作系统内核，如图所示。通常，容器运行速度更快，以裸机性能运行应用程序，并且更易于管理，因为进行操作系统内核调用没有额外的开销。
