---
title: "[docker学习笔记]ubuntu下docker的学习"
date: 2022-10-17
categories: [Linux]
tags:
  - docker学习笔记
  - Ubuntu
description: docker 的简单介绍 docker 官方的定义:Docker 是一个利用容器供开发人员和管理人员开发,部署和运行应用的平台.实际上docker 是一个基于LXC 的高级容器引擎。听起来是不是不知道在说什么？简单地说，docker
---

### docker 的简单介绍

docker 官方的定义:Docker 是一个利用容器供开发人员和管理人员开发,部署和运行应用的平台.实际上docker 是一个基于LXC 的高级容器引擎。听起来是不是不知道在说什么？简单地说，docker 是一个轻量级的虚拟解决方案，或者说 —— 一个超轻量级的虚拟机。

##### docker 的三要素

  1. 镜像  
Docker 镜像可以看作是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）。镜像不包含任何动态数据，其内容在构建之后也不会被改变。
  2. 容器  
容器由运行一个镜像而来,是镜像的一个实例化.类似于运行中的虚拟机.结构上类似与镜像加读写层
  3. 仓库  
仓库是集中存放镜像的地方,对镜像进行版本管理等.类似与git的仓库管理,管理的对象是镜像.



### docker 安装

（１）卸载旧版本
    
    
    sudo apt-get remove docker docker-engine docker.io containerd runc
    

（２）安装依赖
    
    
    sudo apt update
    sudo apt-get install ca-certificates curl gnupg lsb-release
    

（３） 安装GPG证书
    
    
    curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
    

（４）写入软件源信息
    
    
    sudo add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
    

（５）安装新版本
    
    
    sudo apt-get install docker-ce docker-ce-cli containerd.io
    

（６）用户组相关（这步比较重要，否则后续查看docker版本的时候会有报错提示并且也无法启动docker：Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get “http://%2Fvar%2Frun%2Fdocker.sock/v1.24/version”: dial unix /var/run/docker.sock: connect: permission denied）
    
    
    sudo groupadd docker       #添加docker用户组
    sudo gpasswd -a XXX docker     #将当前用户添加至docker用户组，其中XXX为用户名
    newgrp docker       #更新docker用户组
    

（７）启动 docker 服务
    
    
    systemctl start docker
    

（８）安装必要工具
    
    
    apt-get -y install apt-transport-https ca-certificates curl software-properties-common
    

（９）配置 docker  
添加 docker 配置 /etc/docker/daemon.json（需要在目录下自行添加daemon.json）
    
    
    {
    "exec-opts": ["native.cgroupdriver=systemd"],
    "log-driver": "json-file",
    "log-opts": {
    "max-size": "100m"
    },
    "storage-driver": "overlay2",
     "registry-mirrors": ["https://hub-mirror.c.163.com","https://gxeo3yz7.mirror.aliyuncs.com"]
    }
    

（１０）重启docker
    
    
    service docker restart
    

### docker 基础知识及镜像下载知识

[参考](https://www.runoob.com/docker/docker-image-usage.html)

### docker 常见命令

  1. 设置开机启动：systemctl enable docker
  2. 查看docker状态：systemctl status docker
  3. 停止docker服务：systemctl stop docker
  4. 查看docker版本：docker version
  5. 添加docker用户组：sudo groupadd docker 执行以上命令会提示已存在，原因是在安装docker时已自动创建。
  6. 将指定用户添加到用户组（username为你的用户名）：sudo gpasswd -a username docker
  7. 查看是否添加成功：cat /etc/group | grep ^docker
  8. 重启docker：sudo systemctl restart docker
  9. 更新用户组：newgrp docker
  10. 执行docker命令，比如：docker ps -a
  11. 查看容器信息：sudo docker ps -a
  12. 新开标签页：sudo docker exec -ti container_id /bin/zsh
  13. 关闭容器：sudo docker stop container_id
  14. 开启容器：sudo docker start container_id
  15. 删除容器：sudo docker rm container_id
  16. 查看本地镜像 ：sudo docker images
  17. 删除镜像：sudo docker rmi image_id
  18. 容器做成镜像：sudo docker commit container_id image_name[:tag_name]



### 镜像保存，镜像直接导入（一台电脑的镜像，直接导入另一台电脑上使用）

（１）查看已有镜像
    
    
    1、docker images : 列出本地镜像。
    
    语法
    docker images [OPTIONS] [REPOSITORY[:TAG]]
    OPTIONS说明：
    
    -a :列出本地所有的镜像（含中间映像层，默认情况下，过滤掉中间映像层）；
    
    –digests :显示镜像的摘要信息；
    
    -f :显示满足条件的镜像；
    
    –format :指定返回值的模板文件；
    
    –no-trunc :显示完整的镜像信息；
    

（２）在已有镜像系统上导出镜像
    
    
    docker save xxxx镜像名称 > output/xx镜像名称.tar
    或者
    docker save -o  xxxx镜像名称.tar  xxxx镜像名称（xxxx镜像名称 是一个已经存在的镜像）
    

（３）将docker镜像拷贝到对应系统

（４）导入镜像
    
    
    docker load --input  xx镜像名称.tar
    
    或者
    
    docker load < /images/ubuntu_14.04.tar
    

（５）查看镜像是否导入成功
    
    
    执行命令：docker images
    
    docker images -a
    

（６）在容器中运行镜像
    
    
    # 在新建 test 容器中运行导入镜像
    
    docker run -it -p 8899:8899 --name test  xx镜像名称
    

备注:
    
    
    -it：表示交互式终端的容器，非启动后立刻结束的容器
    
    -p 8899 :8899 ：前面为8899为docker的端口，映射到Linux虚拟机的8899 端口
    
    --name test：给容器取个名字，嫌麻烦可以省去
    
    xx镜像名称.tar：容器是用哪个镜像启动的（一个容器，必须依赖一个镜像启动）
    

#### 创建/运行新容器
    
    
    docker run -it -p 8899:8899 --name test xx镜像名称
    
    -v 内外映射
    -v /opt:/tmp/test 外部 /opt 目录映射到　容器内　/tmp/test
    
    
    
        -a stdin: 指定标准输入输出内容类型，可选 STDIN/STDOUT/STDERR 三项；
    
        -d: 后台运行容器，并返回容器ID；
    
        -i: 以交互模式运行容器，通常与 -t 同时使用；
    
        -P: 随机端口映射，容器内部端口随机映射到主机的端口
    
        -p: 指定端口映射，格式为：主机(宿主)端口:容器端口
    
        -t: 为容器重新分配一个伪输入终端，通常与 -i 同时使用；
    
        --name="nginx-lb": 为容器指定一个名称；
    
        --dns 8.8.8.8: 指定容器使用的DNS服务器，默认和宿主一致；
    
        --dns-search example.com: 指定容器DNS搜索域名，默认和宿主一致；
    
        -h "mars": 指定容器的hostname；
    
        -e username="ritchie": 设置环境变量；
    
        --env-file=[]: 从指定文件读入环境变量；
    
        --cpuset="0-2" or --cpuset="0,1,2": 绑定容器到指定CPU运行；
    
        -m :设置容器使用内存最大值；
    
        --net="bridge": 指定容器的网络连接类型，支持 bridge/host/none/container: 四种类型；
    
        --link=[]: 添加链接到另一个容器；
    
        --expose=[]: 开放一个端口或一组端口；
    
        --volume , -v: 绑定一个卷

---

> 本文由 [CSDN 博客](https://blog.csdn.net/weixin_41596275/article/details/127319919) 迁移至本站。
