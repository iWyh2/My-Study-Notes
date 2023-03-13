# Docker

![](https://logos-world.net/wp-content/uploads/2021/02/Docker-Logo-2013-2015.png)

# Docker概念

* Docker是一个开源的应用容器引擎

* 诞生于2013年，由**Go语言**实现
* Docker可以让开发者打包他们的应用以及依赖包到一个轻量级，可移植的容器中，然后发布到任何流行的Linux机器上
* 容器时完全使用沙箱机制，相互隔离
* 容器性能开销极低
* Docker从17.03版本分为CE社区版和EE企业版

【**Docker是一种容器技术，解决软件跨环境迁移问题**】

------



# Docker安装

[Docker官网](https://www.docker.com)

Docker可以安装在所有的主流操作系统上

**Linux**(CentOS7)

```shell
#1、yum包更新到最新 
yum update
#2、安装需要的软件包， yum-util 提供yum-config-manager功能，另外两个是devicemapper驱动依赖的 
yum install -y yum-utils device-mapper-persistent-data lvm2
#3、设置yum源
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
#4、安装docker，出现输入的界面都按 y 
yum install -y docker-ce
#5、查看docker版本，验证是否验证成功
docker -v
```

------



# Docker架构

![](https://www.twle.cn/static/i/docker/docker_architecture_3.png)

**镜像**（Image）：Docker镜像，相当于是一个完整的root文件系统。比如官方镜像Ubuntu:16.04就包含了一整套Ubuntu 16.04最小系统的root文件系统

**容器**（Container）：镜像和容器的关系，就像是面向对象的类和对象，镜像是静态的定义，容器是镜像运行的实体。容器可以被创建，启动，停止，删除，停止

**仓库**（Repository）：仓库就像一个代码控制中心，用来保存镜像

------



# Docker配置镜像加速

一般情况下，从[Docker hub](https://hub.docker.com/)下载的docker镜像太慢，都会**配置镜像加速器**

* USTC：[中科大镜像加速器](https://mirrors.ustc.edu.cn/centos/)
* **阿里云**
* 网易云
* 腾讯云

我们选择使用阿里云提供的【容器镜像服务 ACR】：每个阿里云账号都独有一个镜像加速地址，登录即可查看

1. 安装／升级Docker客户端

   推荐安装1.10.0以上版本的Docker客户端

2. 配置镜像加速器

   针对Docker客户端版本大于 1.10.0 的用户

   可以通过修改daemon配置文件/etc/docker/daemon.json来使用加速器

   ```shell
   sudo mkdir -p /etc/docker
   sudo tee /etc/docker/daemon.json <<-'EOF'
   {
     "registry-mirrors": ["https://xxxxxx.mirror.aliyuncs.com"]
   }
   EOF
   sudo systemctl daemon-reload
   sudo systemctl restart docker
   ```

------



# Docker命令

## 1. daemon 服务相关命令

```shell
#启动Docker服务
systemctl start docker
#停止Docker服务
systemctl stop docker
#重启Docker服务
systemctl restart docker
#查看Docker服务状态
systemctl status docker
#设置开机启动Docker服务
systemctl enable docker
```

## 2. image 镜像相关命令

```shell
#查看镜像
docker images
docker images -q #查看所有镜像id
#搜索镜像，从网络中查找
docker search 镜像名称（如：docker search redis）
#拉取镜像，从Docker仓库下载镜像到本地。下载镜像格式为 名称:版本号，如果不指定版本号，那么就是最新版本latest。需要下载什么可以到Docker hub查询
docker pull 镜像名称(名称:版本号)
#删除镜像
docker rmi 镜像id  #删除指定id的本地镜像
docker rmi `docker images -q`  #删除所有本地镜像
```

## 3. container 容器相关命令

```shell
#查看容器
docker ps     #查看正在运行的容器
docker ps -a  #查看所有的容器
#创建容器并启动
#参数说明：
#1. -i：保持容器运行。通常和‘-t‘同时使用。
#2. -t：为容器重新分配一个伪输入终端，通常与‘-i’使用。
#   -it：两个参数后，容器创建后自动进入容器。退出容器（exit命令）后，容器自动关闭。创建的容器为交互式容器
#3. -d：以守护后台模式运行容器。创建一个容器在后台运行，需要使用'docker exec'命令进入容器。退出容器（exit命令）后，容器不会关闭。
#   -id：创建的容器为守护式容器
#4. --name：为创建的容器命名
docker run 参数
#进入容器
docker exec 参数  #退出容器，守护式容器不会关闭，交互式容器会关闭。参数同上，-it 或 -id。同时指定好进入容器的初始化指令
#示例1：docker run -it --name=c1 centos:7 /bin/bash(进入容器的初始化指令)
#示例2：docker run -id --name=c2 centos:7（创建好了一个容器）docker exec -it c2 /bin/bash（进入刚才创建的容器）

#停止容器
docker stop 容器名称
#启动容器
docker start 容器名称
#删除容器，如果容器在运行，那么会删除失败，需要停止容器
docker rm 容器名称（或容器id）  #删除指定单个容器
docker rm `docker ps -aq`   #删除全部容器
#查看容器信息
docker inspect 容器名称
```

更多命令，可前往[菜鸟教程-Docker命令大全](https://www.runoob.com/docker/docker-command-manual.html)查看

------



# Docker容器的数据卷

## 1. 数据卷概念及作用

前提思考：

* Docker容器删除之后，在容器之中产生的数据会随之销毁
* Docker容器和外部机器（即容器和Windows主机）是不可以直接交换文件的
* 容器之间是相互隔离的，无法进行数据交互



**数据卷**：**为宿主机中的一个目录或者文件**（宿主机为虚拟机中的Linux）

* 当容器目录和数据卷目录绑定之后，对方的修改会立即同步到自身（既解决了容器数据的持久化和容器和外部机器的文件交互）
* 一个数据卷可以被多个容器同时挂载（既解决了容器之间的数据交互）
* 一个容器也可以挂载多个数据卷



**数据卷作用**：

1. 容器数据持久化
2. 外部机器和容器之间的通信
3. 容器之间交换数据

## 2. 配置数据卷

创建启动容器时，使用"-v"参数设置数据卷

```shell
docker run ... -v 宿主机目录或文件:容器内目录或文件
```

【注】1. 目录必须是**绝对路径**。 2. 目录不存在会**自动创建**。 3. 可以**挂载多个**数据卷，既使用多个"-v"参数。

## 3. 配置数据卷容器

多容器之间进行数据交换的方式：

1. 多个容器挂载同一个数据卷
2. 数据卷容器

![](.\imgs\dataContainer.png)

创建启动一个数据卷容器c3，使用"-v"参数设置数据卷

```shell
docker run -it --name=c3 -v /volume centos:7 /bin/bash
```

创建启动c1 c2容器，使用"--volumes-from"参数设置数据卷

```shell
docker run -it --name=c1 --volumes-from c3 centos:7 /bin/bash
docker run -it --name=c2 --volumes-from c3 centos:7 /bin/bash
```

------



# Docker应用部署

## 1. MySQL部署

需求：在Docker容器中部署MySQL，并通过Windows的DataGrip操作MySQL Server

存在问题：**容器内部的网络服务和外部机器（Windows主机）不能直接通信**

但是：外部机器和宿主机（虚拟机的Linux）可以直接通信

所以：当容器中的网络服务需要被外部机器访问时，可以将容器中提供的服务的端口**映射**到宿主机的端口上。外部机器访问宿主机的该端口，从而间接的访问容器内的服务。这种操作叫【**端口映射**】

<<<<<<< HEAD
![](.\imgs\portMap.png)
=======
![](D:\Git\GitLocalRepository\Java\10-Docker\imgs\portMap.png)
>>>>>>> e5e4669570559531accb2d50b48e9cadaca48b52

实现步骤：

1. 搜索mysql镜像

   ```shell
   docker search mysql
   ```

2. 拉取mysql镜像

   ```shell
   docker pull mysql:5.6
   ```

3. 创建容器，设置端口映射、目录映射

   ```shell
   # 在/root目录下创建mysql目录用于存储mysql数据信息
   mkdir ~/mysql
   # 进入到mysql目录下
   cd ~/mysql
   ```

   ```shell
   # 创建并启动mysql容器
   docker run -id \
   -p 3307:3306 \
   --name=c_mysql \
   -v $PWD/conf:/etc/mysql/conf.d \
   -v $PWD/logs:/logs \
   -v $PWD/data:/var/lib/mysql \
   -e MYSQL_ROOT_PASSWORD=123456 \
   mysql:5.6
   ```

   参数说明：

   * **-p 3307:3306**：将容器的 3306 端口映射到宿主机的 3307 端口。
<<<<<<< HEAD
   * **-v $PWD/conf:/etc/mysql/conf.d**：将主机当前目录（PWD）下的 conf/my.cnf 挂载到容器的 /etc/mysql/my.cnf。配置目录
=======
   * **-v $PWD/conf:/etc/mysql/conf.d**：将主机当前目录下的 conf/my.cnf 挂载到容器的 /etc/mysql/my.cnf。配置目录
>>>>>>> e5e4669570559531accb2d50b48e9cadaca48b52
   * **-v $PWD/logs:/logs**：将主机当前目录下的 logs 目录挂载到容器的 /logs。日志目录
   * **-v $PWD/data:/var/lib/mysql** ：将主机当前目录下的data目录挂载到容器的 /var/lib/mysql 。数据目录
   * **-e MYSQL_ROOT_PASSWORD=123456：**初始化 root 用户的密码。

4. 进入容器，操作mysql

   ```shell
   docker exec –it c_mysql /bin/bash
   ```

5. 使用外部机器连接容器中的mysql

   ![1573636765632](.\imgs\1573636765632.png)

【注】连接时记得虚拟机开放3307端口或者暂时关闭防火墙（systemctl stop firewalld）

## 2. Tomcat部署

需求：在Docker中部署Tomcat，并通过外部机器访问Tomcat部署的项目

实现步骤：

1. 搜索tomcat镜像

   ```shell
   docker search tomcat
   ```

2. 拉取tomcat镜像

   ```shell
   docker pull tomcat
   ```

3. 创建容器，设置端口映射、目录映射

   ```shell
   # 在/root目录下创建tomcat目录用于存储tomcat数据信息
   mkdir ~/tomcat
   cd ~/tomcat
   ```

   ```shell
   docker run -id --name=c_tomcat \
   -p 8080:8080 \
   -v $PWD:/usr/local/tomcat/webapps \
   tomcat 
   ```

   参数说明：

   - **-p 8080:8080：**将容器的8080端口映射到主机的8080端口

     **-v $PWD:/usr/local/tomcat/webapps：**将主机中当前目录挂载到容器的webapps

4. 使用外部机器访问tomcat

![1573649804623](./imgs\1573649804623.png)

## 3. Nginx部署

需求：在Docker中部署Nginx，然后外部机器访问

实现步骤：

1. 搜索nginx镜像

   ```shell
   docker search nginx
   ```

2. 拉取nginx镜像

   ```shell
   docker pull nginx
   ```

3. 创建容器，设置端口映射、目录映射

   ```shell
   # 在/root目录下创建nginx目录用于存储nginx数据信息
   mkdir ~/nginx
   cd ~/nginx
   mkdir conf
   cd conf
   # 在~/nginx/conf/下创建nginx.conf文件,粘贴下面内容
   vim nginx.conf
   ```

   ```shell
   user  nginx;
   worker_processes  1;
   
   error_log  /var/log/nginx/error.log warn;
   pid        /var/run/nginx.pid;
   
   
   events {
       worker_connections  1024;
   }
   
   
   http {
       include       /etc/nginx/mime.types;
       default_type  application/octet-stream;
   
       log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                         '$status $body_bytes_sent "$http_referer" '
                         '"$http_user_agent" "$http_x_forwarded_for"';
   
       access_log  /var/log/nginx/access.log  main;
   
       sendfile        on;
       #tcp_nopush     on;
   
       keepalive_timeout  65;
   
       #gzip  on;
   
       include /etc/nginx/conf.d/*.conf;
   }
   ```

   ```shell
   #回退到nginx目录
   cd ..
   #再创建
   docker run -id --name=c_nginx \
   -p 80:80 \
   -v $PWD/conf/nginx.conf:/etc/nginx/nginx.conf \
   -v $PWD/logs:/var/log/nginx \
   -v $PWD/html:/usr/share/nginx/html \
   nginx
   ```

   参数说明：

   - **-p 80:80**：将容器的 80端口映射到宿主机的 80 端口。
   - **-v $PWD/conf/nginx.conf:/etc/nginx/nginx.conf**：将主机当前目录下的 /conf/nginx.conf 挂载到容器的 :/etc/nginx/nginx.conf。配置目录
   - **-v $PWD/logs:/var/log/nginx**：将主机当前目录下的 logs 目录挂载到容器的/var/log/nginx。日志目录

4. 使用外部机器访问nginx

![1573652396669](.\imgs\1573652396669.png)

## 4. Redis部署

需求：在Docker中部署Redis，并通过外部机器访问Redis

实现步骤：

1. 搜索redis镜像

   ```shell
   docker search redis
   ```

2. 拉取redis镜像

   ```shell
   docker pull redis:5.0
   ```

3. 创建容器，设置端口映射

   ```shell
   docker run -id --name=c_redis -p 6379:6379 redis:5.0
   ```

4. 使用外部机器连接redis（Windows的Redis安装目录下，cmd输入，启动reids的客户端）

   ```shell
   ./redis-cli.exe -h 192.168.88.92 -p 6379
   ```

------



# Dockerfile

## 1. Docker镜像原理

思考：

* Docker镜像的本质是什么？
* Docker的CentOS镜像只有200MB，但操作系统的iso文件却有几个G，为什么？
* Docker的Tomcat镜像有500MB，但安装包文件却只有几十MB，为什么？



【知识补充】

操作系统组成部分：

* 进程调度子系统
* 进程通信子系统
* 内存管理子系统
* 设备管理子系统
* **文件管理子系统**
* 网络通信子系统
* 作业控制子系统

Linux文件系统由【bootfs】和【rootfs】组成（fs == filesystem）

![](https://tse4-mm.cn.bing.net/th/id/OIP-C.CI82L2wk7XCnPdGpwuHzvgHaDr?pid=ImgDet&rs=1)

* bootfs：包含bootloader（引导加载程序）和kernel内核
* rootfs：root文件系统，包含典型Linux系统的/dev /proc /bin /etc等标准文件和目录

**不同的Linux发行版，bootfs一样，而rootfs不一样**



**Docker镜像原理**：

1. Docker镜像是**由特殊的文件系统叠加而成的**
2. 最低端为bootfs，使用宿主机的bootfs（所以加载非常快）
3. 第二层为rootfs，为基础镜像
4. 再往上可以叠加其他的镜像文件
5. 同一文件系统（Union File System）技术可以将不同层整合成一个文件系统，为这些层提供了一个统一的视角，这样就隐藏了多层的存在。在用户看来，只存在一个文件系统
6. 一个镜像可以放在另一个镜像上面。位于下面的为父镜像，最底部的为基础镜像
7. 当从一个镜像启动容器时，Docker会在最顶层加载一个读写文件系统作为容器，这个容器可以被更改写为新的镜像

![](https://img-blog.csdnimg.cn/2020031819363410.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0pobm85OQ==,size_16,color_FFFFFF,t_70)

【分层的意义：**复用**】

* Docker镜像的本质是什么？
  * 是一个分层文件系统
* Docker的CentOS镜像只有200MB，但操作系统的iso文件却有几个G，为什么？
  * CentOS的iso文件包含bootfs和rootfs，Docker的centos镜像复用了操作系统的bootfs，只有rootfs和其他镜像层，所以小
* Docker的Tomcat镜像有500MB，但安装包文件却只有几十MB，为什么？
  * Docker镜像为分层的，tomcat自身只有七十多MB，但是需要依赖其他镜像，比如jdk，rootfs等，所以大

## 2. Dockerfile概念及作用

**Docker镜像制作**：

1. 容器转为镜像

   ```shell
   #就容器转为镜像，不可传输
   docker commit 容器id 镜像名称:版本号
   #将镜像压缩为压缩文件，用于传输
   docker save -o 压缩文件名称(xxxx.tar) 镜像名称:版本号
   #将压缩文件加载为镜像使用
   docker load -i 压缩文件名称
   ## 此方法缺点 ##
   # 挂载的文件内容是不会被写入到压缩文件中的（需要重新挂载）
   # 但是在容器内写的文件数据是会被写入到压缩文件中的
   ```

![](.\imgs\DockerImages.png)

2. **Dockerfile**

   概念：**是一个文本文件，用于构建镜像**

   * 包含了一条条指令
   * 每一条指令构建一层，基于基础镜像，最终构建出一个新的镜像

   作用：

   * 对于开发人员，为开发团队**提供一个完全一致的开发环境**
   * 对于测试人员，可以直接使用Dockerfile文件**构建完全一样的环境测试**
   * 对于运维人员，在部署时可以**实现应用的无缝移植**

## 3. Dockerfile关键字

| 关键字      | 作用                     | 备注                                                         |
| ----------- | ------------------------ | ------------------------------------------------------------ |
| FROM        | 指定父镜像               | 指定dockerfile基于哪个image构建                              |
| MAINTAINER  | 作者信息                 | 用来标明这个dockerfile谁写的                                 |
| LABEL       | 标签                     | 用来标明dockerfile的标签 可以使用Label代替Maintainer 最终都是在docker image基本信息中可以查看 |
| RUN         | 执行命令                 | 执行一段命令 默认是/bin/sh 格式: RUN command 或者 RUN ["command" , "param1","param2"] |
| CMD         | 容器启动命令             | 提供启动容器时候的默认命令 和ENTRYPOINT配合使用.格式 CMD command param1 param2 或者 CMD ["command" , "param1","param2"] |
| ENTRYPOINT  | 入口                     | 一般在制作一些执行就关闭的容器中会使用                       |
| COPY        | 复制文件                 | build的时候复制文件到image中                                 |
| ADD         | 添加文件                 | build的时候添加文件到image中 不仅仅局限于当前build上下文 可以来源于远程服务 |
| ENV         | 环境变量                 | 指定build时候的环境变量 可以在启动的容器的时候 通过-e覆盖 格式ENV name=value |
| ARG         | 构建参数                 | 构建参数 只在构建的时候使用的参数 如果有ENV 那么ENV的相同名字的值始终覆盖arg的参数 |
| VOLUME      | 定义外部可以挂载的数据卷 | 指定build的image那些目录可以启动的时候挂载到文件系统中 启动容器的时候使用 -v 绑定 格式 VOLUME ["目录"] |
| EXPOSE      | 暴露端口                 | 定义容器运行的时候监听的端口 启动容器的使用-p来绑定暴露端口 格式: EXPOSE 8080 或者 EXPOSE 8080/udp |
| WORKDIR     | 工作目录                 | 指定容器内部的工作目录 如果没有创建则自动创建 如果指定/ 使用的是绝对地址 如果不是/开头那么是在上一条workdir的路径的相对路径 |
| USER        | 指定执行用户             | 指定build或者启动的时候 用户 在RUN CMD ENTRYPONT执行的时候的用户 |
| HEALTHCHECK | 健康检查                 | 指定监测当前容器的健康监测的命令 基本上没用 因为很多时候 应用本身有健康监测机制 |
| ONBUILD     | 触发器                   | 当存在ONBUILD关键字的镜像作为基础镜像的时候 当执行FROM完成之后 会执行 ONBUILD的命令 但是不影响当前镜像 用处也不怎么大 |
| STOPSIGNAL  | 发送信号量到宿主机       | 该STOPSIGNAL指令设置将发送到容器的系统调用信号以退出。       |
| SHELL       | 指定执行脚本的shell      | 指定RUN CMD ENTRYPOINT 执行命令的时候 使用的shell            |

## 4. 案例

【需求1】：自定义CentOS7镜像，使默认登录路径为/usr，且可以使用vim

【实现步骤】：

* 编写Dockerfile

1. 定义父镜像

   ```dockerfile
   FROM centos:7
   ```

2. 定义作者信息

   ```dockerfile
   MAINTAINER iWyh2 <iwyh2@qq.com>
   ```

3. 执行安装vim命令

   ```dockerfile
   RUN yum install -y vim
   ```

4. 定义默认工作目录

   ```dockerfile
   WORKDIR /usr
   ```

5. 定义容器启动执行的命令

   ```dockerfile
   CMD /bin/bash
   ```

* 执行命令构建镜像

  ```shell
  docker build -f ./centos7_dockerfile -t iwyh2_centos:7 .
  ```

  参数说明：

  * -f：指定dockerfile文件所在目录
  * -t：指定新镜像的名称和版本号
  * 点：指定镜像构建过程中的上下文环境的目录

【需求2】：发布SpringBoot项目

【实现步骤】：

* 准备一个SpringBoot项目：HelloWorld-0.0.1-SNAPSHOT.jar

* 编写Dockerfile

  1. 定义父镜像

     ```dockerfile
     FROM java:8
     ```

  2. 定义作者信息

     ```dockerfile
     MAINTAINER iWyh2 <iwyh2@qq.com>
     ```

  3. 将jar包添加到容器，并改名为app.jar

     ```dockerfile
     #HelloWorld-0.0.1-SNAPSHOT.jar未加路径，代表这个jar包和Dockerfile在同一目录下
     ADD HelloWorld-0.0.1-SNAPSHOT.jar app.jar
     ```

  4. 定义容器启动执行的命令

     ```dockerfile
     CMD java -jar app.jar
     ```

* 执行命令构建镜像

  ```shell
  docker build -f ./springbootapp_helloword_dockerfile -t helloworldapp:1.0.0 .
  ```

【本机虚拟机存储Dockerfile文件的位置：/root/dockerfiles】

------



# Docker服务编排

## 1. 服务编排概念

微服务架构的应用系统中，一般包含若干个微服务。每个微服务一般会部署多个实例，如果每个微服务手动启停，维护工作量会很大

1. 从DockerHub拉取镜像或者Dockerfile构建镜像
2. 创建多个容器
3. 管理这些若干容器

**服务编排**：按照一定的业务规则批量管理容器

## 2. Docker Compose概述

Docker Compose是一个编排多容器分布式部署的工具。

**提供命令集管理**容器化应用的完整开发周期，包括服务构建，启动，停止。

使用步骤：

1. 利用Dockerfile定义运行环境镜像
2. 使用docker-compose.yml定义组成应用的各服务
3. 运行docker-compose up启动应用

![](https://www.adictosaltrabajo.com/wp-content/uploads/2015/11/docker_compose_imagen3-1024x543.png)

## 3. 案例

【使用docker compose编排nginx+springboot项目】

**安装Docker Compose**

```shell
# Compose目前已经完全支持Linux、Mac OS和Windows，在我们安装Compose之前，需要先安装Docker。下面我们以编译好的二进制包方式安装在Linux系统中。 
curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
# 设置文件可执行权限 
chmod +x /usr/local/bin/docker-compose
# 查看版本信息 
docker-compose -version
```

* **卸载Docker Compose**

  ```shell
  # 二进制包方式安装的，删除二进制文件即可
  rm /usr/local/bin/docker-compose
  ```

**编排nginx+springboot项目**

* 创建docker-compose目录

```shell
mkdir ~/docker-compose
cd ~/docker-compose
```

* 编写 docker-compose.yml 文件

```shell
vim docker-compose.yml
```

```yml
# 版本
version: '3'
# 服务编排
services:
  # 名称随便起-nginx
  nginx:
   # 镜像
   image: nginx
   # 端口映射
   ports:
    - 80:80
   # 当前容器访问的项目
   links:
    - app
   # 目录映射 数据卷
   volumes:
    - ./nginx/conf.d:/etc/nginx/conf.d
  # 名称随便起-app
  app:
    # 镜像
    image: helloworldapp:1.0.0
    # 对外访问端口
    expose:
      - "8080"
```

* 创建./nginx/conf.d目录

```shell
mkdir -p ./nginx/conf.d
```

* 在./nginx/conf.d目录下 编写iwyh2.conf文件

```shell
cd ./nginx/conf.d
vim iwyh2.conf
```

```shell
server {
    listen 80;
    access_log off;

    location / {
        proxy_pass http://app:8080;
    }
}
```

* 在~/docker-compose 目录下 使用docker-compose 启动容器

```shell
cd ../../
docker-compose up
```

* 测试访问

```apl
http://192.168.88.92/hello
```

【注】80端口不要被占用了 不然会启动失败

------



# Docker私有仓库

Docker官方的[DockerHub](https://hub.docker.com/)是一个用于管理公共镜像的仓库，我们可以从上面拉取镜像到本地，也可以把我们自己的镜像推送上去。

但是有时我们的服务器无法访问互联网，或者我们不希望自己的镜像放到公网中，那么我们就需要搭建私有仓库来存储和管理我们自己的镜像

## 1. 搭建私有仓库

```shell
# 1、拉取私有仓库镜像 
docker pull registry
# 2、启动私有仓库容器 
docker run -id --name=registry -p 5000:5000 registry
# 3、打开浏览器 输入地址http://私有仓库服务器ip:5000/v2/_catalog，看到{"repositories":[]} 表示私有仓库 搭建成功
http://192.168.88.92:5000/v2/_catalog
# 4、修改daemon.json   
vim /etc/docker/daemon.json    
# 在上述文件中追加一个key，保存退出。此步用于让 docker 信任私有仓库地址；注意将"私有仓库服务器ip"修改为自己私有仓库服务器真实ip 
{
	...,
	"insecure-registries":["192.168.88.92:5000"]
} 
# 5、重启docker 服务 
systemctl restart docker
docker start registry
```

【注】5000端口记得开放，或者暂时关闭防火墙

## 2. 上传镜像到私有仓库

```shell
# 1、标记镜像为私有仓库的镜像，此时本地镜像中，有两个镜像id一样的镜像
docker tag centos:7 私有仓库服务器IP:5000/centos:7
# 2、上传标记的镜像     
docker push 私有仓库服务器IP:5000/centos:7
```

## 3. 从私有仓库拉取镜像

```shell
#拉取镜像 
docker pull 私有仓库服务器ip:5000/centos:7
```

## 4. 删除私有仓库的镜像

```shell
#registry v2版本的镜像，默认是不允许删除镜像的，所以需要修改配置文件，使能删除方法
#查看registry容器ID：7548b35eb4a8
docker ps -a
#查看配置文件
docker exec -it 7548b35eb4a8 cat /etc/docker/registry/config.yml
#修改配置文件，storage段中增加 delete段，将enabled设置为true
docker exec -it 7548b35eb4a8 vi /etc/docker/registry/config.yml
------------------
  delete:
    enabled: true
------------------
#重启容器生效配置
docker restart 7548b35eb4a8
#查看镜像列表
curl http://192.168.88.92:5000/v2/_catalog
#获取镜像摘要信息
curl --header "Accept: application/vnd.docker.distribution.manifest.v2+json" -I -X GET http://192.168.88.92:5000/v2/iwyh2_centos/manifests/7
#根据获取的Docker-Content-Digest删除镜像
curl -I -X DELETE http://192.168.88.92:5000/v2/iwyh2_centos/manifests/sha256:9e11c63a4ec9128c4b79c501a44cd87a2a0ed77014aad3b833ee946aac9de7c5
#检查镜像已经不存在
curl --header "Accept: application/vnd.docker.distribution.manifest.v2+json" -I -X GET http://192.168.88.92:5000/v2/iwyh2_centos/manifests/7
#404 Not Found即为删除成功
#运行docker提供的垃圾回收命令
docker exec -it 7548b35eb4a8 sh -c 'registry garbage-collect /etc/docker/registry/config.yml'
#删除仓库容器下面的镜像目录
docker exec registry rm -rf /var/lib/registry/docker/registry/v2/repositories/iwyh2_centos
#垃圾回收
docker exec registry bin/registry garbage-collect /etc/docker/registry/config.yml
#重启仓库
docker restart registry
#重新查看镜像列表
curl http://192.168.88.92:5000/v2/_catalog
#没有了，ok 删除成功
```

------



# Docker相关概念

**容器**就是将软件打包成标准化单元，用于开发，交付，部署

* 容器镜像是轻量级，可执行的独立软件包，包含软件运行所需的所有内容：代码，运行时环境，系统工具，系统库，设置
* 容器化软件在任何环境中都可以始终如一的运行
* 容器赋予了软件独立性，使其免受外在环境的差异影响，从而有助于团队间在相同基础设施上运行不同的软件时的冲突

![](.\imgs\containerVM.png)



## Docker容器虚拟化与传统虚拟机比较

虚拟机：![](https://www.twle.cn/static/i/docker/docker_architecture_2.png)

容器：![](https://www.twle.cn/static/i/docker/docker_architecture_1.png)

相同：

* 容器虚拟机具有**相似的资源隔离和分配优势**

不同：

1. **容器虚拟化的是操作系统**，虚拟机虚拟化的是硬件
2. 传统虚拟机可以运行不同的操作系统，而**容器只能运行同一类型操作系统**

|    特性    |        容器        |     虚拟机     |
| :--------: | :----------------: | :------------: |
|    启动    |        秒级        |     分钟级     |
|  硬盘使用  |      一般为MB      |    一般为GB    |
|    性能    |      接近原生      |       弱       |
| 系统支持量 | 单机支持上千个容器 | 一般几个就顶天 |

------

> © 2023 iWyh2
