# 运维日常工作

该文档用于阐述蛋壳运维小组的日常工作内容，主要包括：项目部署、服务器监控、蛋壳服务检查、客户项目定期检查以及排期等。

## 项目部署

### 蛋壳项目部署

项目代码托管平台：[Dankal-Gitlab](https://git.dankal.cn/)。

添加服务：在 [Gitlab-deploy](https://git.dankal.cn/deploy) 项目组中迭代维护各端的服务集。

- WEB：[docker-web](https://git.dankal.cn/deploy/docker-web)
- PHP：[docker-php](https://git.dankal.cn/deploy/docker-php)
- Java EE: [docker-javaee](https://git.dankal.cn/deploy/docker-javaee)

CI 流程：[《Jenkins 持续构建流程》](https://git.dankal.cn/manage/documents/blob/master/deploy/jenkins.md)。

如果项目服务端有使用 Redis，请迭代维护 [《Redis 项目映射表》](https://git.dankal.cn/manage/documents/blob/master/deploy/redis_app_map.md)。

### 客户服务器部署

详见[《客户服务器上的项目部署流程》](https://git.dankal.cn/manage/documents/blob/master/deploy/deploy.md)。

## 服务器监控

在[阿里云·云监控](https://cloudmonitor.console.aliyun.com/index.htm?custom_trace=ecs_console#/hostmonitor/host)中，选择主机监控，勾选所有实例（目前有四台主机），然后点击全景图，进行**盯屛**。

选择1分钟刷新，警戒线：CPU：85%，内存：85%，如下图所示：

![cloudmonitor](https://cdn.dankal.cn/deploy/cloudmonitor.png)

如有发现超过警戒线，并且持久不下的情况，应该及时反馈，组织排查服务器异常。

## 蛋壳服务检查

每天上班第一件事，需要检查 [内网穿透服务](https://frps.dankal.cn) 是否正常。

在 Proxies 中分别选择 TCP 和 HTTP，目前共有6条隧道。如下图所示：

HTTP：

![frp-http](https://cdn.dankal.cn/deploy/frp-http.png)

TCP：

![frp-tcp](https://cdn.dankal.cn/deploy/frp-tcp.png)

注意看 status 一般都为 `online`，如果发现显示 `offline`，应该及时反馈并解决。

## 客户项目定期检查

【待定】

在项目交付后的一年免费维护时间内，根据项目部提供的项目维护排期表，对线上项目进行定期检查。比如访问前端页面、后台管理、接口测试，检查域名 SSL 证书是否过期等（考虑单元测试，脚本自动化测试等）。

**这里的检查只针对服务是否正常运行，不针对业务功能上的 BUG。**

一年的维护期后，如果客户需要我们继续维护，则应当另签合同，我们再根据以往的方案继续维护。

## 排期

【待定】

运维小组人员进行工作上的排期，主要针对服务器监控、服务检查、项目检查。

