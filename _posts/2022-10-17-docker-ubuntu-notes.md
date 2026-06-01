---
title: "[docker学习笔记]ubuntu下docker的学习"
date: 2022-10-17
categories: [Linux]
tags:
  - docker学习笔记
  - Ubuntu
description: Ubuntu 下 Docker 入门：三要素、安装步骤、常用命令与镜像导入导出。
---

> 本文属于 **Linux / Ubuntu 专题** 系列。

## 📌 简介

Docker 是轻量级容器平台。本文记录在 Ubuntu 上安装 Docker、常用命令，以及镜像 save/load 等实践笔记。

---

## Docker 简介

Docker 官方定义：供开发与运维**构建、部署、运行应用**的容器平台；基于 LXC，可理解为**超轻量级虚拟机**。

---

## Docker 三要素

1. **镜像**：只读模板，包含运行所需程序、库与配置。
2. **容器**：镜像的运行实例，类似「运行中的虚拟机」。
3. **仓库**：集中存放镜像，支持版本管理（类似 Git 管代码，但对象是镜像）。

---

## Docker 安装

1. 卸载旧版本

```bash
sudo apt-get remove docker docker-engine docker.io containerd runc
```

2. 安装依赖

```bash
sudo apt update
sudo apt-get install ca-certificates curl gnupg lsb-release
```

3. 安装 GPG 证书

```bash
curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
```

4. 写入软件源

```bash
sudo add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
```

5. 安装 Docker

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

6. 用户组（避免 `permission denied` 无法访问 docker.sock）

```bash
sudo groupadd docker
sudo gpasswd -a "$USER" docker
newgrp docker
```

7. 启动服务

```bash
sudo systemctl start docker
```

8. 配置镜像加速（可选，编辑 `/etc/docker/daemon.json`）

```json
{
  "registry-mirrors": ["https://hub-mirror.c.163.com", "https://gxeo3yz7.mirror.aliyuncs.com"]
}
```

9. 重启

```bash
sudo systemctl restart docker
```

---

## 常用命令速查

| 用途 | 命令 |
| --- | --- |
| 开机自启 | `systemctl enable docker` |
| 查看状态 | `systemctl status docker` |
| 查看版本 | `docker version` |
| 列出容器 | `docker ps -a` |
| 进入容器 | `docker exec -it <container_id> /bin/bash` |
| 启停容器 | `docker start/stop <container_id>` |
| 删除容器 | `docker rm <container_id>` |
| 本地镜像 | `docker images` |
| 删除镜像 | `docker rmi <image_id>` |
| 容器转镜像 | `docker commit <container_id> <name:tag>` |

更多说明见 [菜鸟教程 · Docker 镜像](https://www.runoob.com/docker/docker-image-usage.html)。

---

## 镜像导出与导入

1. 导出：`docker save -o myimage.tar myimage:tag`
2. 拷贝 tar 到目标机器
3. 导入：`docker load -i myimage.tar`
4. 验证：`docker images`
5. 运行：`docker run -it -p 8899:8899 --name test myimage:tag`

---

> 📎 **说明**：本文由 [CSDN「小树笔记」](https://blog.csdn.net/weixin_41596275/article/details/127319919) 迁移至 GitHub Pages，并在本站持续维护。安装步骤与镜像源请以当前官方文档为准。
