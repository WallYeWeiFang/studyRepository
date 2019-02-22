# Spring Boot简要概述

## 一. Spring Boot简介
1. 最受Java开发者喜好的框架当属Spring，Spring也成为了在Java EE开发中真正意义上的标准，但是随着新技术的发展，脚本语言大行其道的时代（Node JS，Ruby，Groovy，Scala等），Java EE使用Spring逐渐变得笨重起来，大量的XML文件存在与项目中，繁琐的配置，整合第三方框架的配置问题，低下的开发效率和部署效率等等问题。

2. 这些问题在不断的社区反馈下，Spring团队也开发出了相应的框架：Spring Boot。Spring Boot可以说是至少近5年来Spring乃至整个Java社区最有影响力的项目之一，也被人看作是：Java EE开发的颠覆者（但是不是有点too young，too simple的感觉！）。

3. spring 官方网站本身使用Spring 框架开发，随着功能以及业务逻辑的日益复杂，应用伴随着大量的XML配置文件以及复杂的Bean依赖关系。
随着Spring 3.0的发布，Spring IO团队逐渐开始摆脱XML配置文件，并且在开发过程中大量使用“约定优先配置”（convention over configuration）的思想来摆脱Spring框架中各种复杂的配置，衍生了Java Config。

4. Spring Boot正是在这样的一个背景下被抽象出来的开发框架，它本身并不提供Spring框架的核心特性以及扩展功能，只是用于快速、敏捷地开发新一代基于Spring框架的应用程序。也就是说，它并不是用来替代Spring的解决方案，而是和Spring框架紧密结合用于提升Spring开发者体验的工具。同时它集成了大量常用的第三方库配置（例如Jackson, JDBC, Mongo, Redis, Mail等等），Spring Boot应用中这些第三方库几乎可以零配置的开箱即用（out-of-the-box），大部分的Spring Boot应用都只需要非常少量的配置代码，开发者能够更加专注于业务逻辑。


5. Spring Boot不生成代码，且完全不需要XML配置。其主要目标如下：

  - 为所有的Spring开发工作提供一个更快、更广泛的入门经验。

  - 开箱即用，你也可以通过修改默认值来快速满足你的项目的需求。

  - 提供了一系列大型项目中常见的非功能性特性，如嵌入式服务器、安全、指标，健康检测、外部配置等。



## 二. Spring Boot解决的问题
(1) Spring Boot使编码变简单

(2) Spring Boot使配置变简单

(3) Spring Boot使部署变简单

(4) Spring Boot使监控变简单

(5) Spring的不足



## 三. Spring Boot的优点
Spring Boot继承了Spring的优点，并新增了一些新功能和特性：   

（1）SpringBoot是伴随着Spring4.0诞生的，一经推出，引起了巨大的反向；

（2）从字面理解，Boot是引导的意思，因此SpringBoot帮助开发者快速搭建Spring框架；  

（3）SpringBoot帮助开发者快速启动一个Web容器；  

（4）SpringBoot继承了原有Spring框架的优秀基因；  

（5）SpringBoot简化了使用Spring的过程；  

（6）Spring Boot为我们带来了脚本语言开发的效率，但是Spring Boot并没有让我们意外的新技术，都是Java EE开发者常见的额技术

## 四. Spring Boot主要特征
（1）遵循“习惯优于配置”的原则，使用Spring Boot只需要很少的配置，大部分的时候我们直接使用默认的配置即可；  

（2）项目快速搭建，可以无需配置的自动整合第三方的框架；  

（3）可以完全不使用XML配置文件，只需要自动配置和Java Config；  

（4）内嵌Servlet容器，降低了对环境的要求，可以使用命令直接执行项目，应用可用jar包执行：java -jar；  

（5）提供了starter POM, 能够非常方便的进行包管理, 很大程度上减少了jar hell或者dependency hell；   

（6）运行中应用状态的监控；  

（7）对主流开发框架的无配置集成；  

（8）与云计算的天然继承；
