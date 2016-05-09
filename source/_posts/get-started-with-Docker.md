title: "Docker入门"
date: 2016-04-27 18:25:25  
categories: Docker   
description: "Docker初体验"
tags:
- Docker
- 容器
---
Docker初体验
<!-- more -->
### 安装

判断 ** curl ** 是否安装   
```
which curl   
```

如果没有安装curl，需要执行命令安装

```
sudo apt-get update  
sudo apt-get install curl   
```

获取最新的Docker包

```
curl -fsSL https://get.docker.com/ | sh    
```

国内Daocloud加速

```
curl -sSL https://get.daocloud.io/docker | sh    
```

判断是否安装成功

```
docker version   
```

### 查找镜像 

执行

```
docker run docker/whalesay cowsay boo  
```

会提示

> Unable to find image 'docker/whalesay:latest' locally
然后从Hub下载到本地再运行，第二次运行上述命令则直接运行。

查看本地镜像

```
docker images  
```

### 创建Dockerfile

创建一个新文件夹

```
mkdir mydockerbuild  
cd mydockerbuild  
```

新建名称为Dockerfile的文本文件，添加：

```
FROM docker/whalesay:latest  
RUN apt-get -y update && apt-get install -y fortunes  
CMD /usr/games/fortune -a | cowsay  
```

> FROM 是指定我们再那个镜像的基础上进行的修改  
> apt-get -y  安装fortunes  -y表示同意安装，安装时不会提示 [Y/N] 来让你选择。  
> 运行fortune并把返回的结果传给cowsay  


执行

```
docker build -t docker-whale .   
```

进行创建镜像，看到"Successfully built xxxx"即为创建成功.执行命令查看本地镜像

```
docker images
```

可以看到我们刚刚创建的docker-whale镜像,执行:

```
docker run docker-whale
```

即可运行刚刚创建完成的镜像.

### 发布镜像

```
docker tag 7d9495d03763 maryatdocker/docker-whale:latest

7d9495d03763 为docker images 显示的IMAGE ID
maryatdocker 为DockerHub的用户名
docker-whale 为镜像名称
latest       为版本或者Tag
```

使用下面的命令登陆并输入密码

```
docker login --username=yourhubusername --email=youremail@company.com
```

推送到DockerHub

```
docker push maryatdocker/docker-whale
```

移除本地镜像

```
docker rmi -f 7d9495d03763
7d9495d03763 为docker images 显示的IMAGE ID
```

获取镜像

```
 docker run yourusername/docker-whale
```

### 遇到问题:

>Cannot connect to the Docker daemon. Is the docker daemon running on this host?

解决办法：

```
sudo usermod -aG docker 登陆用户名
```