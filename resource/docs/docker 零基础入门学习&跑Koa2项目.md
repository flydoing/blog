
# docker 零基础入门学习&跑Koa2项目

零基础开始学习一下docker，记录一些过程、笔记

## docker安装与登录

**下载**

直接官网下载回来，跟普通软件的安装是一样：[https://docs.docker.com/](https://docs.docker.com/)

> 备注：浏览器直接下载很慢，建议用迅雷下载；完了跟普通软件一样正常安装就好

**验证安装**


```
# 启动docker后，命令行输入以下、验证安装
$ docker version
$ docker info

# 列出本机的所有 image 文件。
$ docker image ls

# 未创建docker id，会提示登录
$ docker image pull library/hello-word
Using default tag: latest
Error response from daemon: pull access denied for hello-word, repository does not exist or may require 'docker login'

```

所以先去注册登录：
[注册创建docker 账户id](https://hub.docker.com/?utm_source=docker4mac_2.0.0.3&utm_medium=account_create&utm_campaign=referral)

有了账号后，就可以登录了：

```
$ docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: gjincai
Password:
Login Succeeded
```

## 配置国内镜像加速
国内连接 Docker 的官方仓库很慢，可参考这里配置以下：
[配置国内镜像加速](https://github.com/yeasy/docker_practice/blob/master/install/mirror.md)

## 跑个 hello world

登录后，跑个hello world

```
# docker image pull是抓取 image 文件的命令

$ docker image pull library/hello-world

# Docker 官方提供的 image 文件，都放在library组里面，所以它的是默认组，可以省略

$ docker image pull hello-world

# docker container run命令具有自动抓取 image 文件的功能。如果发现本地没有指定的 image 文件，就会从仓库自动抓取。因此，前面的docker image pull命令并不是必需的步骤。

docker container run hello-world

Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
1b930d010525: Pull complete
......

```

列出容器：

```
## 列出本机正在运行的容器:无
docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

## 列出本机所有容器，包括终止运行的容器：刚运行的一个
docker container ls --all
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
80b79d2a0257        hello-world         "/hello"            9 minutes ago       Exited (0) 9 minutes ago                       goofy_ptolemy
```

停止容器运行 `docker container stop`，

终止容器运行 `docker container kill`，

终止运行的容器文件，依然会占据硬盘空间，可以使用 `docker container rm` 命令删除。

```
# 删除刚才的hello world
# docker container rm [containerID]

$ docker container rm 80b79d2a0257
80b79d2a0257

#删完后，变为空
docker container ls --all
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

## 跑个nginx：

```
# 1.run
docker run -d -p 80:80 --name webserver nginx
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
6ae821421a7d: Pull complete
da4474e5966c: Downloading [========================>                          ]  10.69MB/22.23MB
eb2aec2b9c9f: Download complete

# 2.stop rm
docker stop webserver
docker rm webserver

```


## Dockerfile文件创建image镜像: 跑 Koa2 项目

koa2-learn/learn-docker-koa根目录下，建立dockerfile文件：

> [learn-docker-koa源码](https://github.com/gjincai/koa2-learn/tree/master/learn-docker-koa)

```
FROM node:10.15.3
COPY . /app
WORKDIR /app
RUN npm install --registry=https://registry.npm.taobao.org
EXPOSE 3000
```

创建image镜像文件: 在根目录下，build

```
$ docker image build -t learn-docker-koa .
Sending build context to Docker daemon  18.94kB
Step 1/5 : FROM node:10.15.3
10.15.3: Pulling from library/node
e79bb959ec00: Downloading [==============================================>    ]  41.76MB/45.34MB
d4b7902036fe: Download complete
1b2a72d4e030: Download complete
d54db43011fd: Downloading [====================================>              ]  36.07MB/50.07MB
69d473365bb3: Downloading [======>                                            ]   28.9MB/215MB
6e2490ee2dc8: Waiting
6381e1b5feaa: Waiting
b1efb42c63e2: Waiting
```

成功后：

```
$ docker image build -t learn-docker--koa .
Sending build context to Docker daemon  18.94kB
Step 1/5 : FROM node:10.15.3
10.15.3: Pulling from library/node
e79bb959ec00: Pull complete
d4b7902036fe: Pull complete
1b2a72d4e030: Pull complete
d54db43011fd: Pull complete
69d473365bb3: Pull complete
6e2490ee2dc8: Pull complete
6381e1b5feaa: Pull complete
b1efb42c63e2: Pull complete
Digest: sha256:dbe1830d4a8b5ec8226b1cb4a760f9e53696130f76bd4c2e5785e13eba19993b
Status: Downloaded newer image for node:10.15.3
 ---> 64c810caf95a
Step 2/5 : COPY . /app
 ---> 093b0dfaa82b
Step 3/5 : WORKDIR /app
 ---> Running in d43af44ecda5
Removing intermediate container d43af44ecda5
 ---> 7d5203421928
Step 4/5 : RUN npm install --registry=https://registry.npm.taobao.org
 ---> Running in 7351397c23f2
npm WARN learn-docker-koa@1.0.0 No repository field.

added 47 packages from 27 contributors and audited 69 packages in 6.59s
found 0 vulnerabilities

Removing intermediate container 7351397c23f2
 ---> 073c57b66e21
Step 5/5 : EXPOSE 3000
 ---> Running in 04195ab55b16
Removing intermediate container 04195ab55b16
 ---> 9d09a0284622
Successfully built 9d09a0284622
Successfully tagged learn-docker--koa:latest
```

查看image镜像：

```
$ docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
learn-docker-koa    latest              9d09a0284622        About a minute ago   901MB
node                10.15.3             64c810caf95a        3 weeks ago          899MB
nginx               latest              f09fe80eb0e7        2 months ago         109MB
hello-world         latest              fce289e99eb9        3 months ago         1.84kB
```

> image: 删除，重命名，参考以下：
<br> https://stackoverflow.com/questions/25211198/docker-how-to-change-repository-name-or-rename-image
<br> https://github.com/yeasy/docker_practice/blob/master/image/rm.md

## 生成容器：

```
$ docker container run -p 8000:3000 -it learn-docker-koa /bin/bash
root@94e46d12ccab:/app#
```

> <br> -p参数：容器的 3000 端口映射到本机的 8000 端口。
<br> -it参数：容器的 Shell 映射到当前的 Shell，然后你在本机窗口输入的命令，就会传入容器。
<br> learn-docker-koa：image 文件的名字（如果有标签，还需要提供标签，默认是 latest 标签）。
<br> /bin/bash：容器启动以后，内部第一个执行的命令。这里是启动 Bash，保证用户可以使用 Shell。
<br> **注意：** /bin/bash 会把 Dockerfile 的 CMD 命令给覆盖

这表示你已经在容器里面了，返回的提示符就是容器内部的 Shell 提示符。执行下面的命令,执行普通shell命令

```
$ docker container run -p 8000:3000 -it learn-docker-koa /bin/bash
root@94e46d12ccab:/app# ls
Dockerfile  app.js  node_modules  package-lock.json  package.json
root@94e46d12ccab:/app# node -v
v10.15.3
root@94e46d12ccab:/app# node app.js
starting at port 3000...
```

浏览器打开：[http://127.0.0.1:8000/v3/](http://127.0.0.1:8000/v3/)

Ctrl + c 停止 Node 进程，然后按下 Ctrl + d （或者输入 exit）退出容器

## 重启容器

对已经stop的container, 进行start:

```
# 查看当前容器：正在运行的 ls，已停止的 ls --all
docker container ls
docker container ls --all
```

对容器进行操作常用命令：

重启容器：`start,restart,stop,kill`

进入容器：`docker exec -it b5eb4d0bcc6d bash`

> 参考： https://yeasy.gitbooks.io/docker_practice/container/attach_exec.html

进入容器后，正常的bash操作

** Dockerfile 新增 CMD命令：

```
FROM node:10.15.3
COPY . /app
WORKDIR /app
RUN npm install --registry=https://registry.npm.taobao.org
EXPOSE 3000
# CMD node app.js
CMD ["npm", "start"]
```

注意，指定了CMD命令以后，docker container run命令就不能附加命令了（比如前面的/bin/bash），否则它会覆盖CMD命令

```
$ docker container run -p 8000:3000 -it learn-docker-koa-v3 /bin/bash
$ docker container run -p 8000:3000 -it learn-docker-koa-v3
```

## vscode 与 docker

> 参考： [https://github.com/liwei0526vip/vscode/blob/master/30%20!%20%E5%A6%82%E4%BD%95%E5%9C%A8VS%20Code%E4%B8%AD%E9%85%8D%E7%BD%AE%E3%80%81%E9%83%A8%E7%BD%B2%E5%92%8C%E8%B0%83%E8%AF%95Docker%EF%BC%9F.md](https://github.com/liwei0526vip/vscode/blob/master/30%20!%20%E5%A6%82%E4%BD%95%E5%9C%A8VS%20Code%E4%B8%AD%E9%85%8D%E7%BD%AE%E3%80%81%E9%83%A8%E7%BD%B2%E5%92%8C%E8%B0%83%E8%AF%95Docker%EF%BC%9F.md)

## 学习参考

> <br> [阮一峰: Docker 入门教程](http://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)
<br> [https://github.com/yeasy/docker_practice](https://github.com/yeasy/docker_practice)

## 后续学习：

1. dockerFile文件的详细编写
2. 更新版本时，只是更新代码、不用 rebuild 镜像 Image
