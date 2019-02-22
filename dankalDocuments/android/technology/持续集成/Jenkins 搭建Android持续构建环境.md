## Jenkins 搭建Android持续集成环境

**目录**

[toc]

### 什么是持续集成

回顾一下App从打包到测试流程：

1. 待测试的Demo修改版本号
2. 将Demo打包成带签名的Apk
3. 然后使用第三方加固工具进行加固
4. 加固好了之后将Apk上传至fir(或蒲公英)
5. 然后通知测试去fir下载进行测试... 

像这样无味易失误的流程在项目收尾阶段一天可重复多次，明显与我们公司提倡的高效率、敏捷开发的理念背道而驰。 我们应该将更多的时间放在核心的业务逻辑上面，从复杂的集成中解脱出来。于是搭建一个持续集成环境就显得尤为重要了。

![持续集成](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015092301.png)

> 持续集成是一种软件开发实践，即团队开发成员经常集成他们的工作，通常每个成员每天至少集成一次，也就意味着每天可能会发生多次集成。每次集成都通过自动化的构建（包括编译，发布，自动化测试)来验证，从而尽快地发现集成错误。许多团队发现这个过程可以大大减少集成的问题，让团队能够更快的开发内聚的软件。

-------

### 安装

#### Android SDK

由于Jenkins的安装较为简单且环境不一，请自行百度(谷歌)。这里着重讲一下在Linux服务器上如何通过Command line 安装Android SDK。

1. 下载Android SDK(包含SDK Manager、AVD Manager、工具包tools等)
    `wget http://dl.google.com/android/android-sdk_r22.0.5-linux.tgz`
    
2. 将SDK 更新到最新版 (理解为Fetch)
    `tools/android update sdk --no-ui`
    
3. 列出所有SDK版本
    `android list sdk --all`
    
    得到：
    
    ```
     1- Android SDK Tools, revision 25.2.5
     2- Android SDK Platform-tools, revision 25.0.3
     3- Android SDK Build-tools, revision 25.0.2
     4- Android SDK Build-tools, revision 25.0.1
     5- Android SDK Build-tools, revision 25
     6- Android SDK Build-tools, revision 24.0.3
     7- Android SDK Build-tools, revision 24.0.2
     8- Android SDK Build-tools, revision 24.0.1
     9- Android SDK Build-tools, revision 24
     10- Android SDK Build-tools, revision 23.0.3
     11- Android SDK Build-tools, revision 23.0.2
     12- Android SDK Build-tools, revision 23.0.1
     13- Android SDK Build-tools, revision 23 (Obsolete)
     14- Android SDK Build-tools, revision 22.0.1
     15- Android SDK Build-tools, revision 22 (Obsolete)
     16- Android SDK Build-tools, revision 21.1.2
     17- Android SDK Build-tools, revision 21.1.1 (Obsolete)
    ```
    
4. 选择需要更新的SDK

    `android update sdk -u -a -t <package no.>`
    
    例如：
    `android update sdk -u -a -t 1,2,3,4`


参考：

*  http://www.jb51.net/article/109945.htm
*  https://stackoverflow.com/questions/17963508/how-to-install-android-sdk-build-tools-on-the-command-line

#### 插件

在安装Jenkins的过程中，有提示选择插件，在我们默认推荐的插件中，基本包含我们构建项目所需的插件。

* [Git plugin](https://wiki.jenkins.io/display/JENKINS/Git%20Plugin)

> 使得Jenkins拥有Clone、Pull远程仓库等能力，能进行git一系列操作

* [Gradle plugin](https://wiki.jenkins.io/display/JENKINS/Gradle%20Plugin)

> 使得Jenkins拥有打包Apk等能力，可以执行Gradle Task 命令

* [build-name-setter](https://wiki.jenkins.io/display/JENKINS/Build%20Name%20Setter%20Plugin)(非必须)

> 重命名构建名称 (默认为#1，#2)

* [Git Parameter Plug-In](https://wiki.jenkins.io/display/JENKINS/Git%20Parameter%20Plugin)

> 使用Git parameter能够实现选择指定分支进行构建的功能，在需要手动选择标签打包的场景中非常方便。

* [Email Extension Plugin](https://wiki.jenkins.io/display/JENKINS/Email-ext%20plugin)(非必须)

> 发送邮件插件，比系统自带邮件更加强大

* [fir Plugin](https://link.jianshu.com/?t=http://7qn9ic.com1.z0.glb.clouddn.com/fir-plugin-fixed.hpi)

> 打包完成后将APK上传至fir

-------

### 配置

#### Global Tool Configuration(全局工具配置)

* JDK 

    name(别名)：随意
    JAVA_HOME：JDK 路径

* Gradle

    name(别名)：随意
    GRADLE_HOME：xxx/gradle/4.3.1、xxx/gradle/4.1 (可配置多个)

* Git

    name(别名): 随意
    Path to Git executable：xxx/git.exe

#### 全局属性

* 环境变量

    键：ANDROID_HOME
    值：XXX/Android/sdk
    
-------
      
### 打包
#### 新建Job
主页面，`新建` ->`构建一个自由风格的软件项目`即可，点击进入项目配置。
    
#### General
勾选“参数化构建过程(Parameterized construction process)”，点击"添加参数"。

选择“String Parameter”、“Password Parameter” 依次添加`StoreFilePath`、`KeyAlias`、`KeyPassword`、`StorePassword`等参数设置。这一步骤没有唯一标准，可根据项目需要灵活应用。

#### 源码管理
**Git**
Repository URL	: GitLab 项目Http(或Https)Url
Credentials: 选择(添加)GitLab 账号密码

#### 构建触发器
构建触发器用来配置什么时候触发构建，一般做法有手动触发、定时触发、或者提交代码时触发。提交代码触发需要在gitlab中添加webhook。

#### 构建环境
可以通过选中Set Build Name设置构建名称

#### 构建
点击“`添加构建步骤`”，选择“`Invoke Gradle Script`”

![Jenkins构建](http://upload-images.jianshu.io/upload_images/1485186-2239fe28330fa280.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

选择的Gradle Version 与项目Gradle 版本对应

#### 构建后操作
点击“增加构建后操作步骤”，选择“Upload to fir.im”，添加fir.im Token 完成上传Apk到fir的配置


