# Nginx

## 概述

### 介绍

Nginx是一款轻量级的web服务器/反向代理服务器/电子邮件(IMAP/POP3)代理服务器

特点是：内存少 并发能力强

由俄罗斯人 伊戈尔·赛索耶夫 开发

[Nginx官网](https://nginx.org/)

------



### 下载与安装

Linux：

1. 安装依赖包：yum -y install gcc pcre-devel zlib-devel openssl openssl-devel
2. 下载Nginx安装包: wget 资源地址（官网下载即可）
3. 解压：tar -zxvf nginx-1.16.1.tar.gz
4. 进入解压后的目录：cd nginx-1.16.1
5. ./configure --prefix=/home/wyh/wyh/Nginx（安装路径）
6. make && make install

------



### 目录结构

Linux中：使用tree命令即可展示

* conf/nginx.conf：nginx的配置文件
* html：存放前端静态文件
* logs：日志目录，存放日志文件
* sbin/nginx：二进制文件，用于启动，停止nginx

------



## 命令

需要处在Nginx的sbin目录中：

查看nginx版本命令：./nginx -v

启动Nginx之前检查conf/nginx.conf配置文件有没有错：./nginx -t

启动Nginx服务：./nginx

重启Nginx服务：./nginx -s reload（修改了配置文件之后）

停止Nginx服务：./nginx -s stop

启动完成后查看nginx进程：ps -ef | grep nginx



配置系统环境变量 使命令在任何目录都可以使用：vim /etc/profile 加上sbin目录的绝对路径 然后source /etc/profile使其生效

那么之后只需要 nginx -s stop 之类的了

------



## 配置文件结构

**整体结构介绍**

Nginx配置文件（conf/nginx.conf）整体可以分为三个部分：

* 全局块
* Events块
* Http块（配置最频繁的）
  * Http全局块
  * Server块
    * Server全局块
    * location块

【注】http块中可以配置多个server块，每个server块又可以配置多个location块

```shell
#全局块
user  root;
worker_processes  1;

#events块
events {
    worker_connections  1024;
}

#http块
http {
	#http全局块
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;
    
	#server块
    server {
    	#server全局块
        listen       80;           #监听80端口
        server_name  localhost;    #服务器域名 - localhost
        
		#location块
        location / {                       #匹配客户端请求url - /
            root   html;				   #指定静态资源根目录 - html
            index  index.html index.htm;   #默认首页 - index.html index.htm
        }

        error_page   500 502 503 504  /50x.html;
        
        #location块
        location = /50x.html {
            root   html;
        }
    }
}
```

------



## 具体应用

### 部署静态资源

Nginx可以作为静态web服务器来部署静态资源（html，css，js，图片，视频等）

Nginx处理静态资源的能力比Tomcat更高效

部署方式：**将文件复制到Nginx安装目录下的html目录即可**

------



### 反向代理

* 正向代理：**在客户端设置代理服务器**，通过代理服务器转发请求，最终访问到目标服务器

  是一个位于客户端和原始服务器之间的服务器（**客户端是知道有代理服务器的**）

  客服端无法直接向原始服务器发起请求，转而向代理服务器请求，并指定原始服务器为目标，代理服务器向原始服务器转交请求并将获取的内容返回给客户端

  正向代理的用途就是为在防火墙内的局域网客户端提供访问Internet的途径

  客户端 ==> 代理服务器 ==> 原始服务器（例如谷歌等）（客户端 ==X==> 原始服务器）



**反向代理**

反向代理服务器位于客户端和目标服务器之间（一般和目标服务器组成一个内网）

对于客户端来说，反向代理服务器就是目标服务器（客户端访问反向代理服务器就可以获得目标服务器的资源，反向代理服务器只是将请求转发给目标服务器）

客户端是不需要知道目标服务器的地址的（对于目标服务器而言是隐藏了用户的，而用户是不知道访问的是反向代理服务器的）

```txt
					      -----------------------
					      |		===>  web服务器1 |
客户端  ========>	 反向代理服务器  ===>	web服务器2 |
                          |		===>  web服务器3 |
					      -----------------------
```



**配置反向代理**

以Linux虚拟机作为反向代理服务器 访问Windows下的web应用

```ini
server {
	listen	82;
	server_name	localhost;
	location / {
		#反向代理配置，将请求转发到指定服务器
		proxy_pass http://192.168.xx.xx:8080;   #配置转发到哪个目标服务器地址 - Windows主机ip
	}
}
```



【注】以Linux的Nginx作为反向代理服务器，来访问Windows下的Web项目时，如果访问失败，那么可以看自己的Linux下是否开放了指定的配置端口（82），或者暂时关闭了防火墙。同时可能Windows下也要开放对应的Web项目使用的端口（新建防火墙入站规则，开放8080端口）（目前不需要）

------



### 负载均衡

早期的网站流量和业务功能相对比较简单，单台服务器就可以满足基本需求

但是随着互联网的发展，业务流量越来越大，业务逻辑越来越复杂，单台服务器的性能以及单点故障问题就随之出现

因此就需要**多台服务器组成应用集群，进行性能的水平扩展以及避免单点故障的出现**

* 应用集群：将同一应用部署到多台机器上，组成应用集群，接收负载均衡器分发的请求，进行业务的处理并返回响应的数据
* 负载均衡器：将用户的请求根据对应的**负载均衡算法**分发**到应用集群中的一台服务器上进行处理**

```txt
					   ----------------------
					   |	===>  web服务器1 |
客户端  ========>	 负载均衡器 ===>	 web服务器2 |
                       |	===>  web服务器3 |
					   ----------------------
```



**配置负载均衡**

```ini
#配置负载均衡 - 默认轮询算法(轮着来)
#upstream指令可以定义一组服务器
upstream targetServer {
	server 192.168.xx.xx:8080;	#服务器1的ip加端口地址 - Windows主机ip
	server 192.168.xx.xx:8080;	#服务器2的ip加端口地址 - Windows主机ip
}

server {
	listen	8080;
	server_name	localhost;
	location / {
		#反向代理配置，将请求转发到指定服务器
		proxy_pass http://targetServer;   #上面upstream配置的目标服务器组名称 - targetServer(可改)
	}
}
```

这里可以以Linux虚拟机作为负载均衡器 轮询访问Windows下的web应用

【注】如果访问失败，也要注意Linux的防火墙和Windows的入站规则开放端口（目前不需要）



**负载均衡策略**：

|    名称    | 说明                 |
| :--------: | :------------------- |
|    轮询    | 默认的方式，无需配置 |
|   weight   | 按权重方式           |
|  ip_hash   | 依据ip分配           |
|  url_hash  | 依据url分配          |
| least_conn | 依据最少连接方式     |
|    fair    | 依据响应时间方式     |

例如：

```linux
#按权重方式
upstream targetServer {
	server 192.168.xx.xx:8080 weight=10;	#服务器1的ip加端口地址
	server 192.168.xx.xx:8080 weight=15;	#服务器2的ip加端口地址
}
```

------

> ©iWyh2