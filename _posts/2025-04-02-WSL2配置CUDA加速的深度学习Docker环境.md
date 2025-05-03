---
title: "WSL2配置CUDA加速的深度学习Docker环境"
tags: [WSL, CUDA, DL, Docker, CN]
style: fill # fill / border
color: secondary # primary / secondary / success / danger / warning / info / light / dark
description: 基于Eric Moore的文章和官方文档修改
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
