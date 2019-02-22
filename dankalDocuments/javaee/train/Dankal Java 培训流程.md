# Dankal Java 快速培训流程大纲（初稿）

## 一. 第一阶段（为期一天）
### 1. 熟悉 Dankal Java 编程规范
[Dankal Java 编程规范](https://git.dankal.cn/manage/documents/blob/master/javaee/Dankal%20Java%E5%BC%80%E5%8F%91%E8%A7%84%E8%8C%83.md)

### 2. 熟悉 Dankal Java 技术架构

1. 项目框架：SpringBoot

2. ORM框架：Mybatis

3. RESTful框架: Swagger

4. 缓存框架: Redis

5. 版本管理：Maven

6. 代码管理: Git


## 二. 第二阶段（为期一天）
### 2.1 搭建SpringBoot框架：  

以模板项目中的常见问题/热门搜索为实现接口对象。

要求如下：

- 集成Mybatis
- 集成Swagger
- 集成Redis
- 使用Maven管理
- 进行Git工作流

**将本地创建到数据库整理成 sql 存放到项目中**

### 2.2 考核点：
以下考核点后续

1. Mybatis

  - 验收前未完成：0
  - 验收前完成，能够基本集成项目。并进行IUSD等操作： 1
  - 配置mybatis-config.xml ： 2
  - 能够使用Druid作为数据源 ：3
  - 集成Mybatis分页： 4
  - 集成多数据源 ：5

2. Swagger

  - 验收前未完成：0
  - 能够基本显示swagger接口内容以及基本项目信息： 1
  - 能够规范编写接口文档： 2
  - 能够屏蔽不必要Controller： 3
  - 能够配置自定义的heard： 4   

3. Redis

  - 验收前未完成：0
  - 能够在项目中实现并使用redis：1
  - 能够提供基类封装：2
  - 能够配合CacheManage实现redis：3
  - 能够实现Key策略：4
  - 能够实现消息队列：5

4. Maven

  - 验收前未完成：0
  - 能够实现基本导包：1
  - 能够进行包版本控制：2
  - 会用spring-io管理：3

5. Git

  - 验收前未完成：0
  - 能够正常提交项目：1

## 三. 第三阶段（为期一天）
### 3.1 共同开发登录/注册模块。
提供多模块框架开发。

- 均进行各自注册
- 登录方式分配：
  - 方海河：手机登录
  - 范鹏飞：邮箱登录
  - 李  超：账号密码登录
- 均进行各自忘记密码

### 3.2 考核点：
0. 功能是否实现

1. Git workflow

 根据是否出现merge错误评分：低，中，高。  

2. 数据库表创建

  - 字段是否有限制：1
  - 字段是否有备注：2
  - 表命名是否规范：3

3. sql查询

  - 是否规范 ：1
  - 是否优化 ：2

4. 代码是否有注释

  - 是否注释：1
  - 是否格式化代码：2
