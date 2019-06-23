# SpringBoot概述

> 更新时间：2019-02-28

本小结简要概述了SpringBoot参考文档，也可作为其它文档的参考目录。

### 1.关于这篇文档

SpringBoot参考文档支持以下几种文件格式：
- HTML
- PDF
- EPUB

最新的文档可在[ docs.spring.io/spring-boot/docs/current/reference](https://docs.spring.io/spring-boot/docs/current/reference)获得 

保存这篇文档可以给你自己使用也可以分享给其他人，前提是您对此不收取任何费用，并且都包含本版权声明，无论是以印刷品还是电子形式分发。

### 2.获取帮助
如果你对SpringBoot有疑问，我们提供以下帮助：
- 尝试使用[How to](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#howto)文档，里面提供了大多数问题解决方案。
- 学习基本的Spring知识，SpringBoot是基于Spring项目构建的。访问[Spring网站](https://spring.io/)，提供大量的参考文档。如果你想从Spring开始，可以尝试这篇[文档](https://spring.io/guides)
- 在[stackoverflow](https://stackoverflow.com/)上提问，看是否有贴有`spring-boot`标签。
- 在[github](https://github.com/spring-projects/spring-boot/issues)上向我们提问

### 3.第一步
如果你开始使用SpringBoot或Spring，从以下主题开始：
- 从头开始：概述 | 要求 | 安装
- 教程：第一部分 | 第二部分
- 运行案例：第一部分 | 第二部分

### 4.使用SpringBoot
准备在工作中使用SpringBoot了吗？我们为你提供了：
- 系统构建：[Maven](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#using-boot-maven) | [Gradle](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#using-boot-gradle) | [Ant](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#using-boot-ant) | [Starters](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#using-boot-starter)
- 最好的练习：[代码结构](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#using-boot-structuring-your-code) | [ @Configuration注解](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#using-boot-configuration-classes) | [@EnableAutoConfiguration 注解](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#using-boot-auto-configuration) |  [beans和依赖注入](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#using-boot-spring-beans-and-dependency-injection)
- 运行你的代码：[IDE](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#using-boot-running-from-an-ide) | [程序包](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#using-boot-running-as-a-packaged-application) | [Maven](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#using-boot-running-with-the-maven-plugin) | [Gradle](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#using-boot-running-with-the-gradle-plugin)
- 将你的应用打包：[发布jar包](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#using-boot-packaging-for-production)
- SpringBoot CLI：[使用CLI](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#cli)

### 5.了解SpringBoot的一些特性
想要了解SpringBoot更多细节？为你准备了以下内容：
- 核心特征：[Spring应用](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-spring-application) | [外部配置]( https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-external-config) | [profiles配置](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-profilesz) | [日志](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-logging) | 
- Web 应用：[MVC](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-spring-mvc) | [嵌入式容器](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-embedded-container)
- 数据操作：[SQL](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-sql) | [NO-SQL](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-nosql)
- 消息：[概述](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-messaging) | [JMS](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-jms)
- 测试：[概述](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-testing) | [启动应用](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-testing-spring-boot-applications) | [单元测试](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-test-utilities) 
- 扩展：[自动装配](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-developing-auto-configuration) | [条件注解@Conditions](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-condition-annotations)

### 6.发布应用
当你准备在生产环境下发布应用，我们为你准备了一些技巧你可能会喜欢：
- 管理端点：[概述]() | [自定义]()
- 连接选择：[HTTP]() | [JMX]()
- 监测：[指标]() | [审查]() | [路径]() | [流程]()
- 
### 7.高级功能
最后，我们为用户准备了几个高级功能：
- SpringBoot应用部署：[云部署]() | [系统服务]() 
- 构建插件工具：[Maven]() | [Gradle]() 
- 附录：[应用属性]() | [自动装配类]() | [执行jar]() 