# 接入

https://docs.docker.com/

https://www.bilibili.com/video/BV1og4y1q7M4?p=3



## 概念





旧的方式：服务器安装环境，配置，再上传项目运行

Docker

项目 + 环境 一起打包成「Docker镜像」，放到服务器上一次性部署





## 优势

Docker是容器，相比虚拟机更轻量级

隔离，在Docker安装下载使用卸载，不影响宿主机



## 相关定义

* 镜像Image。模板

* 容器Container。实例，相当于小型虚拟机系统

* 仓库Repository。放镜像的地方



## 安装

https://www.kuangstudy.com/bbs/1444983935850278914



# 使用

## Docker命令

https://docs.docker.com/engine/reference/commandline/docker/

https://www.runoob.com/docker/docker-command-manual.html



```sh
-h 帮助

# 搜索镜像
docker search 镜像名

# 下载镜像 
docker pull 镜像名

# 查看镜像
docker images
-q # 仅查看id

# 删除镜像
docker rmi 镜像名

# 删除所有镜像
docker rmi -f $(docker images -qa)



# 创建并运行容器，如果本地没有镜像则拉取
docker run 镜像名
-it 容器 /bin/bash # 进入容器并以bash开始编辑
-d # 后台开启容器
-p 主机端口:容器端口 # 端口映射
-v 主机目录:容器目录 # 目录映射
-e xxx=xxx # 环境配置
--name # 名字
--volumes-from 容器 # 从某个容器同步信息


# 进入运行中容器，显示已有命令状态
docker attach 容器

# 进入运行中容器，并打开新命令行
docker exec -it 容器 /bin/bash

# 查看容器信息
docker inspect 容器

# 查看运行中容器
docker ps
-a # 查看所有
-q # 仅查看id

# 删除容器
docker rm 容器
-f # 强制删除启动中的

# 删除所有容器
docker rm -f $(docker ps -qa)

# 拉取容器中文件/目录到宿主机
docker cp 容器:路径 主机路径



# 使用dockerfile创建镜像，不要少了最后的「.」
docker build -f dockerfile -t 镜像名 .

docker run -it --name co2 --volumes-from co1 bdcentos


```







## 基础用法：

1. 搜索镜像（DockerHub）
2. pull 下载镜像
3. 创建并运行镜像的实例 docker run -d --name -p 宿主机端口:实例的端口 xx



### 自定义镜像

镜像是分层的概念，类似安卓里套娃的一个一个依赖包。

拉取镜像生成实例后，相当于引入依赖，原有的xx层是只读不可变的，自定义修改的内容相当于给予他们上面加一层。

```sh
# 修改容器

# 提交容器成为自定义镜像
docker commit -m "描述" -a "作者" 容器名 目标镜像名:TAG

# 也可以使用 dockerfile 构建（主流）
```



## 容器数据卷

### 介绍

容器=应用+环境

数据不能放容器里，容器删除时会消失



使用卷技术，将容器内目录挂载到宿主机上，自动同步双向绑定。

如果容器删除，主机上的目录不会删除



且容器间可以共享数据



### 使用

```sh
# -v 命令挂载
docker run -v ...
-v 容器目录						# 匿名
-v 卷名:容器目录			 # 具名
-v 主机目录:容器目录		# 指定路径

# 容器目录后 :ro :rw 可以设置权限
ro # 只读，容器无法操作，只能通过宿主机修改
rw # 可读可写

# 启动后inspect可以查看信息，Mounts
docker volume inspect 卷名
```



### 具名和匿名挂载

```sh
# 匿名挂载
docker run -it -v 容器目录 容器

# 具名挂载(主要用这个)
docker run -it -v 卷名:容器目录 容器

# 查看所有 volume 卷
docker volume ls


$ docker run -d -P --name ng2 -v jumnginx:/etc/nginx nginx
DRIVER    VOLUME NAME
local     6e9471da633101f60250a4d38b7d53b1c66a350b9ff506dcaf1968275e111b5b
local     jumnginx
分别是匿名具名挂载

$ docker volume inspect jumnginx
[
    {
        "CreatedAt": "2021-10-15T07:35:58Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/jumnginx/_data",
        "Name": "jumnginx",
        "Options": null,
        "Scope": "local"
    }
]
可以看到默认目录是 /var/lib/docker/volumes/

```



### 数据卷容器

多个mysql同步数据

```sh
# 创建co2，自动从co1同步信息，co1+co2之间都会共享
$ docker run -it --name co2 --volumes-from co1 bdcentos

# 如果再创建co3，从co1挂载，则co123之间都会同步

# 拷贝+同步，如果删除co1，co23中文件还存在且互相同步

# 容器间的数据卷，会在没有任何容器使用时消失。但同步到主机上的，容器删除时不影响主机的
```



## Dockerfile

### 介绍

docker的命令脚本，用于构建docker镜像

以前发布使用jar等，现在最终发布交付的是 dockerfile 文件



步骤：

1. 编写dockerfile
2. docker build 构建成镜像
3. docker run 创建镜像容器运行
4. docker push 发布（DockerHub、阿里云...）



可以参考官方镜像里的写法，搜索某个镜像然后查看Dockerfile links

https://registry.hub.docker.com/

```sh
# centOS dockerfile
FROM scratch
ADD centos-7-x86_64-docker.tar.xz /

LABEL \
    org.label-schema.schema-version="1.0" \
    org.label-schema.name="CentOS Base Image" \
    org.label-schema.vendor="CentOS" \
    org.label-schema.license="GPLv2" \
    org.label-schema.build-date="20201113" \
    org.opencontainers.image.title="CentOS Base Image" \
    org.opencontainers.image.vendor="CentOS" \
    org.opencontainers.image.licenses="GPL-2.0-only" \
    org.opencontainers.image.created="2020-11-13 00:00:00+00:00"

CMD ["/bin/bash"]
```



也可以在docker里直接history命令查看参考

```sh
docker history 镜像名
```



### 构建过程

**基础知识**

1. 指令都必须大写
2. 从上到下执行
3. #注释
4. 每个指令都会创建提交一个新的镜像层

<img src="/Users/lcy/Documents/note/服务端/Docker.assets/image-20211015180350583.png" alt="image-20211015180350583" style="zoom:50%;" />



### 指令

```sh
FROM					# 基础镜像 scratch(最基础的)、centOS ...
MAINTAINER		# 作者，姓名+邮箱
RUN						# 构建时，需要运行的命令
ADD						# 添加内容
WORKDIR				# 工作目录，进入容器时默认目录
VOLUME				# 挂载目录
EXPOSE				# 暴露端口
CMD						# 启动时，需要运行的命令。只有最后一个会生效，可被替代
ENTRYPOINT		# 启动时，需要运行的命令。可以追加命令
ONBUILD				# 当构建一个被继承 DockerFile 时会运行，触发指令
COPY					# 类似ADD，将文件拷贝到镜像中
ENV						# 构建时，设置环境变量
```



### 实践

#### 创建自己的centos

```sh
# 1.编写dockerfile
FROM centos
MAINTAINER boredream<48262906@qq.com>

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 80

CMD echo $MYPATH
CMD echo "----end----"
CMD /bin/bash


# 2.构建镜像
$ docker build -f dockerfile -t vimcentos .

# 3.运行自己的镜像
$ docker run -it --name vcos1 vimcentos /bin/bash

# 4.测试vim命令
$ vim 

测试成功


```



#### 打包SpringBoot项目成镜像

```sh
# 1.项目mvn package

# 2.编写dockerfile
FROM openjdk:8-jdk-alpine
COPY target/*.jar app.jar
ENTRYPOINT ["java","-jar","/app.jar"]

# 3.定位到Dockerfile文件，构建镜像
$ docker build -t 镜像名 .

# 3.运行自己的镜像
$ docker run -it --name 容器名 -p 8080:8080 镜像名 /bin/bash

# 测试运行
$ curl localhost:8080

```



EXPOSE 是镜像暴露端口，不等于实际应用使用端口

比如SpringBoot默认8080，如果封装镜像这里写 8081，则启动的容器8081也可以访问但只有8080是指向SpringBoot应用的





```sh
# 创建dockerfile文件，名字随便，建议 dockerfile
FROM centos

VOLUME ["v1", "v2"]

CMD echo "----- end ----"
CMD /bin/bash

# 每个命令，就是镜像的一层



# 使用dockerfile创建镜像，不要少了最后的「.」
docker build -f dockerfile -t 镜像名 .

# 之后就可以 images 查看镜像了
```



```sh
# 启动自己镜像，进入查看
$ docker run -it boredream/centos:1.0 /bin/bash
$ ls -l

total 56
lrwxrwxrwx   1 root root    7 Nov  3  2020 bin -> usr/bin
drwxr-xr-x   5 root root  360 Oct 15 08:02 dev
drwxr-xr-x   1 root root 4096 Oct 15 08:02 etc
drwxr-xr-x   2 root root 4096 Nov  3  2020 home
lrwxrwxrwx   1 root root    7 Nov  3  2020 lib -> usr/lib
lrwxrwxrwx   1 root root    9 Nov  3  2020 lib64 -> usr/lib64
drwx------   2 root root 4096 Sep 15 14:17 lost+found
drwxr-xr-x   2 root root 4096 Nov  3  2020 media
drwxr-xr-x   2 root root 4096 Nov  3  2020 mnt
drwxr-xr-x   2 root root 4096 Nov  3  2020 opt
dr-xr-xr-x 185 root root    0 Oct 15 08:02 proc
dr-xr-x---   2 root root 4096 Sep 15 14:17 root
drwxr-xr-x  11 root root 4096 Sep 15 14:17 run
lrwxrwxrwx   1 root root    8 Nov  3  2020 sbin -> usr/sbin
drwxr-xr-x   2 root root 4096 Nov  3  2020 srv
dr-xr-xr-x  13 root root    0 Oct 15 08:02 sys
drwxrwxrwt   7 root root 4096 Sep 15 14:17 tmp
drwxr-xr-x  12 root root 4096 Sep 15 14:17 usr
drwxr-xr-x   2 root root 4096 Oct 15 08:02 v1
drwxr-xr-x   2 root root 4096 Oct 15 08:02 v2
drwxr-xr-x  20 root root 4096 Sep 15 14:17 var

v1 v1 就是自己挂载的文件夹

# 可以退出容器查看
$ docker inspect 9d50e2261260 
{
    "Type": "volume",
    "Name": "eb776d77191061040d2f55335f2bea32e172644f5746417890c6e6457149eb49",
    "Source": "/var/lib/docker/volumes/eb776d77191061040d2f55335f2bea32e172644f5746417890c6e6457149eb49/_data",
    "Destination": "v1",
    "Driver": "local",
    "Mode": "",
    "RW": true,
    "Propagation": ""
},
{
    "Type": "volume",
    "Name": "e2bfd3188b126d1059c7089783673b283466890876bfddbd6ba0040c85be32f1",
    "Source": "/var/lib/docker/volumes/e2bfd3188b126d1059c7089783673b283466890876bfddbd6ba0040c85be32f1/_data",
    "Destination": "v2",
    "Driver": "local",
    "Mode": "",
    "RW": true,
    "Propagation": ""
}
```



### cmd区别？

https://www.bilibili.com/video/BV1og4y1q7M4?p=29&spm_id_from=pageDriver



## Docker-compose

### 介绍

多容器综合使用

https://docs.docker.com/compose/

### 安装

Todo centOS



### 使用

```yaml
# 1. 准备好 自己编写的程序（如SpringBoot打包的jar） + Dockerfile

# 2. 编写 docker-compose.yml
version: "3.9"  # 版本
services:
  web:
    build: .	# 使用Dockerfile新生成镜像
    ports:
      - "8080:8080"
    links:
      - mysql
  mysql:
    image: mysql
```



services下是不同的容器，互相隔离。但所有的地址都处于同一个docker网络下，且在docker服务处自动注册了CDN，因此其它应用如SpringBoot中 mysql url可以直接写成 mysql:3306



### 命令

```sh
# 所有 docker-compose 命令都需要在 docker-compose.yml 所在文件夹运行

# 创建并启动所有容器
docker-compose up

# 启动所有容器
docker-compose start

# 关闭所有容器
docker-compose stop

```









# 实践场景

镜像中心，可以搜索查看版本、文档

https://registry.hub.docker.com/





## Mysql

```sh
# 创建mysql
$ docker run --name bd-mysql -e MYSQL_ROOT_PASSWORD=root -d -p 13306:3306 mysql


```



### 多个mysql同步

```sh
# 创建mysql，并设置volume
$ docker run --name mysql1 -e MYSQL_ROOT_PASSWORD=root -d -p 13306:3306 -v /etc/mysql/conf.d -v /var/lib/mysql mysql

# 创建另一个mysql容器，v-from上一个容器
$ docker run --name mysql2 -e MYSQL_ROOT_PASSWORD=root -d -p 13307:3306 --volumes-from mysql1 mysql


```







