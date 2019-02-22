# 客户服务器上的项目部署流程

## 准备工作

- 阿里云服务器购买，选择 CentOS 7.2 操作系统。
- 网络安全组配置，开放必要的端口。
- 域名解析，添加所需的二级域名解析。

## 安装 Docker

```sh
$ yum install -y docker
```

### 检查是否安装成功

```sh
$ docker -v
```

## 安装 Docker Compose

```sh
$ pip install docker-compose
```

如果没有 pip 工具，则使用官方推荐的方式安装：

```sh
$ curl -L https://github.com/docker/compose/releases/download/1.20.1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
$ chmod +x /usr/local/bin/docker-compose
```

### 检查是否安装成功

```sh
$ docker-compose -v
```

## 新建 deploy 用户

```sh
$ useradd deploy
```

修改 deploy 用户密码，统一为 `Dankal123`。

```sh
$ passwd deploy
```

添加 sudo 权限，编辑 sudoers：

```sh
$ vi /etc/sudoers
```

在倒数第十行左右，添加一行配置，允许 deploy 用户 sudo 操作免密码：

```sh
## Same thing without a password
# %wheel	ALL=(ALL)	NOPASSWD: ALL
deploy ALL=(ALL) NOPASSWD: ALL
```

保存退出，切换到 deploy 用户

```sh
$ su deploy
$ cd ~
```

## 登录 Docker 镜像仓库

首先开启服务器上的 docker 服务：

```sh
$ sudo systemctl start docker.service
```

然后检查能否与蛋壳 Harbor 仓库通讯：

```sh
$ ping harbor.dankal.cn
PING harbor.dankal.cn (10.29.251.143) 56(84) bytes of data.
64 bytes from dankal.ecs (10.29.251.143): icmp_seq=1 ttl=64 time=0.029 ms
64 bytes from dankal.ecs (10.29.251.143): icmp_seq=2 ttl=64 time=0.070 ms
64 bytes from dankal.ecs (10.29.251.143): icmp_seq=3 ttl=64 time=0.056 ms
64 bytes from dankal.ecs (10.29.251.143): icmp_seq=4 ttl=64 time=0.070 ms
^C
--- harbor.dankal.cn ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 2999ms
rtt min/avg/max/mdev = 0.029/0.056/0.070/0.017 ms
```

如上结果可以 ping 通，则可以登录 Harbor：

```sh
$ sudo docker login --username=deploy --password=Dankal123 harbor.dankal.cn
Login Succeeded
```

如果 ping 结果如下，一直没有得到响应，则无法登录 Harbor。

```sh
$ ping harbor.dankal.cn
PING harbor.dankal.cn (10.29.251.143) 56(84) bytes of data.
```

需要使用备用的方案，选择阿里云容器镜像仓库：

```sh
$ sudo docker login --username=eggshell_studio@163.com --password=Dankal123 registry.cn-shenzhen.aliyuncs.com
Login Succeeded
```

## 编排容器

### 工作目录

在客户服务器上，统一运维目录为 `/home/deploy`，与蛋壳服务器一致。其它目录一律不动，维护上做到绝对的集中，也避免与客户服务器原有资源和配置的冲突。

在此目录创建 docker 目录，用于存放 docker-compose.yml 文件，各个端有所不同，与蛋壳服务器一致。

前端：

```sh
$ mkdir -p /home/deploy/docker/web
```

PHP：

```sh
$ mkdir -p /home/deploy/docker/php
```

JAVA:

```sh
$ mkdir -p /home/deploy/docker/javaee
```

### Docker Compose 配置

进入目录，并创建 docker-compose.yml 文件，例如 JAVA 的：

```sh
$ cd /home/deploy/docker/javaee
$ touch docker-compose.yml
```

`vi` 编辑 docker-compose.yml 文件，把蛋壳服务器上已经编排好的容器服务配置拷贝过来，主要分为三部分。

> 以下的示例镜像为阿里云镜像仓库的，实际情况如果可以 ping 通 harbor，则更换为 harbor 的镜像。

#### 上层代理

Nginx 代理服务的配置，通用，无需修改。

```yaml
version: '2'
services:
  nginx-root:
    restart: always
    image: registry.cn-shenzhen.aliyuncs.com/dankal/nginx
    container_name: nginx-root
    ports:
      - 80:80
      - 443:443
    volumes:
      - /etc/nginx/conf.d
      - /etc/nginx/vhost.d
      - /usr/share/nginx/html
      - /var/run/docker.sock:/tmp/docker.sock:ro
      - /home/deploy/nginx/certs:/etc/nginx/certs:ro
  nginx-gen:
    restart: always
    image: registry.cn-shenzhen.aliyuncs.com/dankal/nginx-gen
    container_name: nginx-gen
    volumes_from:
      - nginx-root
    volumes:
      - /var/run/docker.sock:/tmp/docker.sock:ro
    command: -notify-sighup nginx-root -watch -wait 5s:30s /etc/docker-gen/templates/nginx.tmpl /etc/nginx/conf.d/default.conf
  nginx-certbot:
    restart: always
    image: registry.cn-shenzhen.aliyuncs.com/dankal/nginx-certbot
    container_name: nginx-certbot
    environment:
      - NGINX_DOCKER_GEN_CONTAINER=nginx-gen
      - NGINX_PROXY_CONTAINER=nginx-root
    volumes_from:
      - nginx-root
    volumes:
      - /home/deploy/nginx/certs:/etc/nginx/certs:rw
      - /var/run/docker.sock:/var/run/docker.sock:ro
```

#### 环境依赖

一般部署数据库，缓存服务等。

```yaml
  mysql-dev:
    restart: always
    image: registry.cn-shenzhen.aliyuncs.com/dankal/mysql
    container_name: mysql-dev
    volumes:
      - /home/deploy/data/mysql-dev:/var/lib/mysql
    ports:
      - 3306:3306
  mysql-stage:
    restart: always
    image: registry.cn-shenzhen.aliyuncs.com/dankal/mysql
    container_name: mysql-stage
    volumes:
      - /home/deploy/data/mysql-stage:/var/lib/mysql
    ports:
      - 3307:3306
  mysql-prod:
    restart: always
    image: registry.cn-shenzhen.aliyuncs.com/dankal/mysql
    container_name: mysql-prod
    volumes:
      - /home/deploy/data/mysql-prod:/var/lib/mysql

  redis-dev:
    restart: always
    image: registry.cn-shenzhen.aliyuncs.com/dankal/redis
    container_name: redis-dev
    volumes:
      - /home/deploy/data/redis-dev:/data
    ports:
      - 6379:6379
  redis-stage:
    restart: always
    image: registry.cn-shenzhen.aliyuncs.com/dankal/redis
    container_name: redis-stage
    volumes:
      - /home/deploy/data/redis-stage:/data
    ports:
      - 6380:6379
  redis-prod:
    restart: always
    image: registry.cn-shenzhen.aliyuncs.com/dankal/redis
    container_name: redis-prod
    volumes:
      - /home/deploy/data/redis-prod:/data
```

上面列举了 mysql 和 redis 的全环境编排，注意各个服务的 volumes，都需要将保存持久化数据的目录挂载到宿主机上，否则可能造成数据的丢失。

映射的公网端口如下：

- mysql-dev 3306
- mysql-stage 3307
- redis-dev 6379
- redis-stage 6380

如果无法通过宿主机地址和上述端口连接容器服务，请检查防火墙或者阿里云后台的安全策略。

为了安全性，生产环境的端口不映射到宿主机上。


#### 项目部署

最后一块是项目的部署，基本与蛋壳服务器的编排一致。

```yaml
  javaee-bsd-dev:
    restart: always
    image: registry.cn-shenzhen.aliyuncs.com/dankal/javaee-bsd-dev
    container_name: javaee-bsd-dev
    environment:
      - VIRTUAL_HOST=api-bsd-dev.dankal.cn
      - LETSENCRYPT_HOST=api-bsd-dev.dankal.cn
    links:
      - redis-dev:redis
      - mysql-dev:mysql
  javaee-bsd-stage:
    restart: always
    image: registry.cn-shenzhen.aliyuncs.com/dankal/javaee-bsd-stage
    container_name: javaee-bsd-stage
    environment:
      - VIRTUAL_HOST=api-bsd-stage.dankal.cn
      - LETSENCRYPT_HOST=api-bsd-stage.dankal.cn
    links:
      - redis-stage:redis
      - mysql-stage:mysql
  javaee-bsd-prod:
    restart: always
    image: registry.cn-shenzhen.aliyuncs.com/dankal/javaee-bsd-prod
    container_name: javaee-bsd-prod
    environment:
      - VIRTUAL_HOST=api-bsd.dankal.cn
      - LETSENCRYPT_HOST=api-bsd.dankal.cn
    links:
      - redis-prod:redis
      - mysql-prod:mysql
```

**注意**：

与蛋壳服务器上的编排配置不同的是，不使用 `external_links`，而是 `links`。

因为在蛋壳服务器上 mysql 和 redis 等都编排为 common 组的服务，跟项目不在同一个服务组里，即不在同一个 docker-compose.yml 文件中，所以才用 `external_links`。客户服务器上只使用一个组，组内容器间通讯使用 `links` 即可。

另外，项目代码中的 mysql 和 redis 的 hostname 都不写域名或者IP，请直接写服务别名，即 `mysql`、`redis`。

## 启动服务

结合原有 Jenkins 的工作流，在 Jenkins 上打包，构建镜像，推送到镜像仓库，ssh 连接到客户该服务器，启动服务。

这里只贴一下相关的 Execute shell script on remote host using ssh 的 Command：

```sh
cd ~/docker/javaee;
sudo docker kill javaee-bsd-stage;
sudo docker rm javaee-bsd-stage;
sudo docker rmi registry.cn-shenzhen.aliyuncs.com/dankal/javaee-bsd-stage;
sudo docker-compose up -d
```

至此，已完成所有的服务部署。


