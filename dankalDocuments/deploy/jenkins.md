# Jenkins 持续构建流程

所有项目都必须使用 [Jenkins](https://build.dankal.cn) 持续构建流程：

- 在 Jenkins 上拉取 [Gitlab](https://git.dankal.cn) 指定分支的项目源代码。
- 安装项目依赖（打包）。
- 构建 Docker 镜像，并将 Docker 镜像 Push 到蛋壳镜像仓库 [Harbor](https://harbor-public.dankal.cn)。
- 使用 SSH 插件连上服务器，使用 docker-compose 重新部署容器，完成项目服务的部署。
- 在 Gitlab 上给对应的项目添加 webhook，实现持续构建。

## General

### Project name

项目名要添加项目前缀，规范如下：

| 项目 | 前缀 |
| --- | --- |
| 前端 | web_ |
| PHP | php_ |
| Java EE | javaee_ |

### Description

描述也要写清楚，至少需要补充项目全称。

### Discard old builds

抛弃旧的构建记录，只保持两条记录。

也就是需要在 `Max # of builds to keep` 填 `2`。

## Source Code Management

**Git** - **Repositories** 填上 **Repository URL**，请填写 SSH 地址，例如 `git@git.dankal.cn:javaee/template.git`。 

**Credentials** 统一选择 `dankal (deploy)`。

> 如果此处有报错，请检查并确保项目属于 Groups，而不是个人项目。

**Branches to build** 一般需要填开发分支，比如 `*/dev` 或者 `*/develop`，因为多数情况是开发环境所用。各个端的约定可以有所不同，比如使用 `*/release` 等分支。如果此构建是生产环境下的，则填 `*/master`。

## Build Triggers

不需要勾选任何选项，只需要在 gitlab 上添加对应的项目添加 webhook。

在项目 Settings - Integrations 填上 URL，格式为：`https://{user}:{token}@build.dankal.cn/job/{job_name}/build`。

- `{user}` 替换为固定值 `dankal_deploy`。
- `{token}` 替换为固定值 `27f6e331d8a7e9f5fa2d47df307077c7`。
- `{job_name}` 替换为 Jenkins 对应的 Project name，例如 `javaee_bsd`。

URL 示例：`https://dankal_deploy:27f6e331d8a7e9f5fa2d47df307077c7@build.dankal.cn/job/javaee_bsd/build`。

Trigger 默认只需要勾上 Push events，SSL verification 默认勾上 Enable SSL verification，最后点击 Add webhook 即可。

## Build

不同项目的构建操作不尽相同。

### PHP

#### 安装依赖

首先添加构建步骤 **Execute shell**，安装项目依赖：

**Command**

```sh
composer install
```

#### 构建镜像

然后添加 **Build / Publish Docker Image**，构建项目镜像：

**Directory for Dockerfile** 默认留空即可，也可填 `.` （一般 Dockerfile 都放在项目根目录下）。

**Cloud** 选择 `docker`。

**Image** 填上阿里云镜像全路径，格式为 `{host}/{namespace}/{image_name}`。
    
- `{host}` 固定填 `harbor.dankal.cn`。
- `{namespace}` 命名空间，各个端对应可选值 `javaee`、`php`、`web`。
- `{image_name}` 填镜像名称，例如 `hxlg`。

示例：`harbor.dankal.cn/php/hxlg`。

**Push image** 勾上。

**Registry Credentials** 选择 `deploy/******(deploy for harbor.)`。

**Clean local images** 勾上。

**Attempt to remove images when jenkins deletes the run** 勾上。

#### 部署服务

最后添加构建步骤 **Execute shell script on remote host using ssh**，通过 SSH 连上服务器，进行容器服务部署：

**SSH site** 选择 `deploy@10.29.251.143:12188`。

**Command** 示例:

```sh
cd /home/deploy/docker/php;
sudo docker stop php-hxlg;
sudo docker rm php-hxlg;
sudo docker rmi harbor.dankal.cn/php/hxlg;
sudo docker-compose up -d php-hxlg
```

> 每行命令最后面有个英文的分号 `;`
> 
> 每个项目只需要改每行最后的一个单词，例如上述例子中的 `php`、`php-hxlg`、`hxlg`。
> 不要再出现复制粘贴然后改错的情况。

**Execute each line** 不要勾上。

### Node

首先添加构建步骤 **Execute shell**，使用 yarn 安装 js 依赖。

**Command**

```sh
yarn install
yarn run build
```

接下来操作都与上文 PHP 的操作类似，此处只贴一个 **Execute shell script on remote host using ssh** 示例：

```sh
cd /home/deploy/docker/web;
sudo docker stop web-frontend-template;
sudo docker rm web-frontend-template;
sudo docker rmi harbor.dankal.cn/web/frontend-template;
sudo docker-compose up -d web-frontend-template
```

### Springboot

只有第一步安装依赖有所不同，这里只列举 Maven 和 Gradle 的安装 jar 包依赖的命令。
构建镜像和部署服务的操作都跟上述一致。

#### Maven

**Execute shell**

```sh
mvn clean package -DskipTests
```

#### Gradle

**Execute shell**

```sh
gradle bootRepackage -x test
```



