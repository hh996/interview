# 1 基本概念
- 镜像（Image）
- 容器（Container）
- 仓库（Repository）
    ## 1 镜像
    镜像是一个包含应用程序代码、运行时、系统工具、库和设置的只读文件集。镜像是构建容器的基础，它包含了在容器中运行应用程序所需的所有内容。镜像是不可修改的，如果需要对应用程序进行更新或更改，需要构建一个新的镜像。
    ## 2 容器
    容器是镜像的运行实例。容器是轻量级、可移植的，它隔离了应用程序及其依赖项，使其能够在任何支持 Docker 的环境中运行。容器是可读写的，可以在运行时进行修改。一个镜像可以生成多个容器，每个容器都是相互隔离的运行环境。
    ## 3 仓库
    仓库是用于存储 Docker 镜像的地方

# 2 常用命令
获取镜像  
```shell
$ docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]
$ docker pull ubuntu:18.04
```
运行
```shell
$ docker run -it --rm ubuntu:18.04 bash
```
docker run 就是运行容器的命令  
-it:-i：交互式操作，一个是 -t 终端
--rm:这个参数是说容器退出后随之将其删除。  
bash：启动一个 bash 
```shell
docker image ls
```

# 容器
启动镜像，生成容器
```shell
docker run -it ubuntu:18.04 /bin/bash
```
如果使用了 -d 参数运行容器,容器会在后台运行
```shell
docker run -d ubuntu:18.04 /bin/sh -c "while true; do echo hello world; sleep 1; done"
77b2dc01fe0f3f1265df143181e7b9af5e05279a884f4776ee75350ea9d8017a
```
终止容器
```shell
docker container stop 9218f20d20fe
```
进入容器
```shell
docker attach 243c
```
导入导出
```shell
docker export 7691a814370e > ubuntu.tar
docker import 
```
删除容器
```shell
docker container rm trusting_newton # 删除一个
docker container prune
```