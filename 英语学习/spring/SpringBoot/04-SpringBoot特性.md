# SpringBoot特性

> 更新时间：2019-03-30（未完结）

这一小节我们将深入了解SpringBoot的细节。你能了解到你想要使用或定义的关键功能。如果你还没有准备好，你可以能需要阅读下[“第二部分：快速开始”](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#getting-started)和[“第三部分：使用SpringBoot”](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#using-boot)小节，这样你会有了良好的基础。

### 23. SpringApplication
SpringApplication类提供了一种快捷方式可以从`main()`方法启动。在大多数情况下，你可以委托给静态`SpringApplication.run`方法，如下所示：
```
public static void main(String[] args) {
	SpringApplication.run(MySpringConfiguration.class, args);
}
```
当你的应用启动后，你会在看到类似一下的内容输出：
```
 .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::   v2.1.3.RELEASE

2013-07-31 00:08:16.117  INFO 56603 --- [           main] o.s.b.s.app.SampleApplication            : Starting SampleApplication v0.1.0 on mycomputer with PID 56603 (/apps/myapp.jar started by pwebb)
2013-07-31 00:08:16.166  INFO 56603 --- [           main] ationConfigServletWebServerApplicationContext : Refreshing org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext@6e5a8246: startup date [Wed Jul 31 00:08:16 PDT 2013]; root of context hierarchy
2014-03-04 13:09:54.912  INFO 41370 --- [           main] .t.TomcatServletWebServerFactory : Server initialized with port: 8080
2014-03-04 13:09:56.501  INFO 41370 --- [           main] o.s.b.s.app.SampleApplication            : Started SampleApplication in 2.992 seconds (JVM running for 3.658)
```

默认情况下，会显示INFO级别的日志信息，包括一些启动的相关细节，如果你需要其它级别的日志信息，你可以查看这小节的相关描述：[“日志等级”](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-custom-log-levels)

### 23.1 启动失败
如果你的应用启动失败，注册的**故障分析**有机会提供一条专门的错误信息和一个具体的措施来解决这个问题。例如，如果你在8080端口启动你的Web应用程序，但是这个端口已经使用，你会看到类似以下信息：
```
***************************
APPLICATION FAILED TO START
***************************

Description:

Embedded servlet container failed to start. Port 8080 was already in use.

Action:

Identify and stop the process that's listening on port 8080 or configure this application to listen on another port.
```
> SpringBoot提供了许多故障分析的程序，并且你可以添加并实现你自己的故障分析程序

如果没有故障分析程序能够处理异常，你仍然可以显示所有的条件信息，以便于更好的了解哪里出了问题。为此，你需要为`rg.springframework.boot.autoconfigure.logging.ConditionEvaluationReportLoggingListener`启动debug属性或者启动debug日志。例如，如果你使用`java -jar`来运行你的程序，你可以启动debug属性，如下所示：
```
java -jar myproject-0.0.1-SNAPSHOT.jar --debug
```

### 23.2 自定义Banner
你可以在classpath中添加一个`banner.txt`文件或在`spring.banner.location`属性中设置一个这样的文件来更改启动时打印机出来的Banner。如果这个文件的编码类型不是UTF-8，你可以在`spring.banner.charset`中设置。除了文本文件之外，你也可以在你的classpath中添加一个`banner.gif`、`baner.jpg`或者`baner.png`图片，或者设置`spring.banner.image.location`属性。
图片被转换成ASCII码以艺术形式表现，并在任何文本baner之前打印。
(ps: 这是我自己实验的，效果不错)
![image](https://raw.githubusercontent.com/liueleven/study/master/%E5%9B%BE%E5%BA%93/17-spring/springboot%E8%87%AA%E5%AE%9A%E4%B9%89banner%E5%9B%BE%E7%89%87%E6%89%93%E5%8D%B0%E6%95%88%E6%9E%9C%E5%9B%BE.png)

在你的`banner.txt`文件中，你可以使用以下任何一个占位符：

### 23.1 Banner 参数

| 参数                                                         | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ${application.version}                                       | 应用程序的版本号，如MANIFEST.MF中声明的。例如，`Implementation-Version: 1.0` 会打印出`1.0` |
| ${application.formatted-version}                             | 应用程序的版本号，如MANIFEST.MF，格式显示为(用括号包围，前缀为v )。例如( v1.0)。 |
| ${spring-boot.version}                                       | 你使用的SpringBoot版本。例如`2.1.3.RELEASE`                  |
| ${spring-boot.formatted-version}                             | 你使用的SpringBoot版本。格式显示为(用括号包围，前缀为v )。例如( v1.0)。例如(v`2.1.3.RELEASE`) |
| ${Ansi.NAME} (或 ${AnsiColor.NAME}, ${AnsiBackground.NAME}, ${AnsiStyle.NAME}) | 其中`NAME`是ANSI转义码的名称。查看更多[细节](https://github.com/spring-projects/spring-boot/tree/v2.1.3.RELEASE/spring-boot-project/spring-boot/src/main/java/org/springframework/boot/ansi/AnsiPropertySource.java) |
| ${application.title}                                         | 应用的标题，如`MANITAL.MF`中声明的。例如，`Implementation-Title: MyApp`被打印为`MyApp`。 |


> 如果你想要通过代码来设置banner可以用`SpringApplication.setBanner(…)`
> 方法来设置。使用`org.springframework.boot.Banner`接口并实现你自己的`printBanner()`方法

您也可以使用`spring.main.banner`模式属性来确定横幅是必须打印在`System.out (控制台)`上，发送到配置的记录器(`日志`)，还是根本不产生(`关闭`)。

打印的banner以以下名称注册为单例bean：`springBootBanner`

> YAML映射为`off`或`false`，所以如果想在你的应用中禁止使用banner，要加引号，如下所示：
```
spring:
	main:
		banner-mode: "off"
```
### 23.3 自定义SpringApplication
如果`SpringApplication`不符合你的口味，你可以自定义一个本地实例来代替它。例如，关闭banner，你可以这样些：
```
public static void main(String[] args) {
	SpringApplication app = new SpringApplication(MySpringConfiguration.class);
	app.setBannerMode(Banner.Mode.OFF);
	app.run(args);
}
```
> 传递给`SpringApplication`的构造函数参数是Spring beans的配置源。在大多数情况下，这些是对@`Configuration`类的引用，但是它们也可以是对XML配置或应该扫描的包的引用。

也可以使用`application.properties`文件来配置`SpringApplication`.更多细节请查看[第二十四章：外部配置](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-external-config)。完整的配置选项列表，查看[SpringBootApplication](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/api/org/springframework/boot/SpringApplication.html)文档

### 23.4 构建流畅的API
如果你想要构建`ApplicationContext`上下文结构（多个上下文具有父子关系）或者你更喜欢使用“fluent”构建API，你可以使用`SpringApplicationBuilder`


`SpringApplicationBuilder`可以让你使用链式调用多个方法包括父方法和子方法来创建层次关系，如下代码所示：
```
new SpringApplicationBuilder()
		.sources(Parent.class)
		.child(Application.class)
		.bannerMode(Banner.Mode.OFF)
		.run(args);
```
> 当你创建`ApplicationContext`层次时会有一些限制。例如，Web组件一定要包含子上下文，并且父子上下文要使用相同环境中，查看[SpringApplicationBuilder](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/api/org/springframework/boot/builder/SpringApplicationBuilder.html)文档了解看多细节。


### 23.5 应用事件和监听器
除了常见的Spring框架事件(如ContextRefreshedEvent )，Spring应用程序还会发送一些额外的应用程序事件。

> 有些事件在ApplicationContext创建之前就被触发，所以你不能注册为@Bean来监听。你可以将他们注册到`SpringApplication.addListeners(...)`或`SpringApplicationBuilder.listeners(…)`方法上。如果你想要的监听器自动被注册，而不管应用如何创建，你可以添加`META-INF/spring.factories`文件到你的项目并使用`org.springframework.context.ApplicationListener`key来引用你的监听器，如下所示：
> org.springframework.context.ApplicationListener=com.example.project.MyListener

当应用程序运行时，应用程序事件按以下顺序发送:
1. `ApplicationStartingEvent`在任何处理之前启动进入，除了监听器注册和初始化之外
2. 当已知要在上下文中使用的环境时，但在创建上下文之前，会进入`ApplicationEnvironmentPreparedEvent`
3. 在刷新开始之前，但在加载bean定义之后，会发送`ApplicationPreparedEvent`
4. 在刷新上下文之后，但在调用任何应用程序和命令行运行程序之前，将发送`ApplicationStartedEvent`
5. 在调用任何应用程序和命令行运行程序后，将进入`ApplicationReadyEvent`它表示应用程序已准备好服务请求
6. 在调用任何应用程序和命令行运行程序后，将进入`ApplicationReadyEvent`它表示应用程序已准备好服务请求。
7. 如果启动时出现异常，则会发送`ApplicationFailedEvent`

你通常不需要使用应用事件，但是知道了对你来说很方便，实际上，SpringBoot内部使用事件来处理不同的任务。

应用程序事件通过使用Spring框架的事件发布机制发送，该机制的一部分确保在子上下文中发布给监听器的事件也发布给任何父上下文中的监听器。因此，如果您的应用程序使用`SpringApplication`实例的层次结构，监听器可能会接收到相同类型应用程序事件的多个实例。

为了让你的监听器能够区分其上下文的事件和后代上下文的事件，它应该请求注入其应用程序上下文，然后将注入的上下文与事件的上下文进行比较。可以通过实现`ApplicationContextAware`来注入上下文，或者，如果侦听器是个bean，可以通过使用`@Autowired来注入上下文`

### 23.6 Web 环境
`SpringApplication`试图代表你创建正确类型的应用程序上下文。用于确定`WebApplicationType`的算法相当简单:
- 如果是Spring MVC，则使用注释配置ServletWebServerApplicationContext
- 如果不是Spring MVC，是Spring WebFlux，则使用`AnnotationConfigReactiveWebServerApplicationContext`
- 否则，将使用`AnnotationConfigApplicationContext`

这意味着，如果在同一应用程序中使用Spring MVC和来自Spring WebFlux的新WebClient，则默认情况下将使用Spring MVC。您可以通过调用`SetWebApplicationType` ( `WebApplicationTyp`e )轻松覆盖这一点。

甚至还可以完全调用`setApplicationContextClass ( ... )`来控制的`ApplicationContext`类型。

> 通常需要调用`SetWebApplicationType` (`WebApplicationType .NONE`)在JUnit测试中使用`SpringApplication`。

### 23.7 访问应用程序参数
如果你需要访问传递给的`SpringApplication.run (...)`的参数，你可以注入一个`org.SpringFramework.boot.ApplicationArguments`的bean,`ApplicationArguments   `接口提供对原始String[]参数以及解析的选项和非选项参数的访问，如下所示:
```
import org.springframework.boot.*;
import org.springframework.beans.factory.annotation.*;
import org.springframework.stereotype.*;

@Component
public class MyBean {

	@Autowired
	public MyBean(ApplicationArguments args) {
		boolean debug = args.containsOption("debug");
		List<String> files = args.getNonOptionArgs();
		// if run with "--debug logfile.txt" debug=true, files=["logfile.txt"]
	}

}
```
> Spring Boot还向Spring环境注册了`CommandLinePropertySource`。这也允许您通过使用`@Value`注解插入单个应用程序参数。


### 23.8 使用`ApplicationRunner`或`CommandLineRunner`
如果你需要再次运行已启动的`SpringApplication`,你可以实现`ApplicationRunner`或`CommandLineRunner`接口。这连个接口以相同的方式工作，并提供一个`run`方法，该方法会在`SpringApplication.run (...)`之前调用。

`CommandLineRunner`接口以简单字符串数组的形式提供对应用程序参数的访问，而`pplicationRunner`使用前面讨论的`ApplicationArguments`接口。以下示例显示了一个带有`run`方法的`CommandLineRunner`:
```
import org.springframework.boot.*;
import org.springframework.stereotype.*;

@Component
public class MyBean implements CommandLineRunner {

	public void run(String... args) {
		// Do something...
	}

}
```
如果定义了几个`CommandLineRunner`或`ApplicationRunner`beans必须按特定顺序调用的 ，你可以实现`Org.SpringFramework.Core.Ordered`接口，或者使用Org.SpringFramework.Core.Annotation.Order`注解。

### 23.9 退出应用
每个`SpringApplication`向JVM注册一个关闭hook(挂钩)，以确保`ApplicationContext`可以优雅的关闭。可以使用所有标准的Spring生命周期回调(例如`DisposableBean`接口或`@PreDestroy`注解)。

另外，如果bean希望调用`SpringApplication.exit()`返回特定的退出码，他们可以实现`org.springframework.boot.ExitCodeGenerator`接口。这个退出码可以被传递到System.exit()，以状态码的形式返回。如下所示：
```
@SpringBootApplication
public class ExitCodeApplication {

	@Bean
	public ExitCodeGenerator exitCodeGenerator() {
		return () -> 42;
	}

	public static void main(String[] args) {
		System.exit(SpringApplication
				.exit(SpringApplication.run(ExitCodeApplication.class, args)));
	}

}
```
此外，`ExitCodeGenerator`接口可以通过异常来实现。当遇到异常时，SpringBoot返回由`getExitCode ( )`方法提供的退出码

### 23.10 管理功能
应用通过指定`spring.application.admin.enable`属性来启动管理相关功能。这暴露了在`MBeanServer`平台上的`SpringApplicationAdminMXBean`。你可以使用这个功能远程管理你的应用。这个功能在任何服务上都是有用的。

> 如果你知道应用程序在哪个HTTP端口运行，可以通过关键词`local.server.port`获取属性

 `注意：启用此功能时要小心，因为MBean公开了关闭应用程序的方法。`

 ### 24. 外部配置
 SpringBoot通过外部配置可以让同样的应用程序代码运行在不同的环境中。你可以使用properties文件，YMAL文件，环境变量，和命令行参数来进行外部配置。属性值可以直接使用注解`@Value`注入，通过Spring`Environment`抽象访问，或者通过`@ConfigurationProperties`绑定相应的对象结构上。

SpringBoot使用一种非常特殊的`PropertySource`顺序，该顺序旨在允许合理地覆盖值。属性按以下顺序考虑:
- [Devtools 全局配置属性](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#using-boot-devtools-globalsettings) 在你的home目录上(`~/.spring-boot-devtools.properties`当devtools被激活)
- [`@TestPropertySource`](https://docs.spring.io/spring/docs/5.1.5.RELEASE/javadoc-api/org/springframework/test/context/TestPropertySource.html)注解在你的测试上
- `properties`属性在你的测试上，[`@SpringBootTest`](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/api/org/springframework/boot/test/context/SpringBootTest.html)和这些[`注解`](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-testing-spring-boot-applications-testing-autoconfigured-tests)可用在你的测试用例上 
- 命令行参数
- SPRING_APPLICATION_JSON中的属性（内嵌json嵌入在环境变量或系统属性中）
- `ServletConfig`初始化参数
- `ServletContext`初始化参数
- JNDI中的属性`comp/env`
- Java 系统属性`System.getProperties()`
- 系统环境变量
- `RandomValuePropertySource`，其属性仅为`Random.*`
- 打包jar之外的应用配置文件（`application-{profile}.properties`和变体的YAML）
- 打包jar中的应用配置文件（`application-{profile}.properties`和变体的YAML）
- 打包jar之外的应用配置文件（`application.properties`和变体的YAML）
- 打包jar中的应用配置文件（`application.properties`和变体的YAML）
- `PropertySource`注解在你的`@Configuration`类上
- 默认属性（设置指定的`SpringApplication.setDefaultProperties`)

为了提供一个具体示例，假设您开发了一个使用name属性的`@Component`，如下例所示:
```
import org.springframework.stereotype.*;
import org.springframework.beans.factory.annotation.*;

@Component
public class MyBean {

    @Value("${name}")
    private String name;

    // ...

}
```
在你应用的classpath上（例如，jar内部）可以有一个`application.properties`文件，该文件为name提供来一个合理的默认值。当你在新的环境中运行，可以在jar之外提供一个`application.properties`文件来覆盖`name`。对于一次性测试，可以使用特定的命令行启动。（例如，`java -jar app.jar --name="Spring"`
> SPRING_APPLICATION_JSON属性可以在命令行上与环境变量一起提供。例如，您可以在un*x shell中使用以下命令行:

    $ SPRING_APPLICATION_JSON='{"acme":{"name":"test"}}' java -jar myapp.jar

> 在前面的示例中，你最终会在Spring环境中进行`acme.name =test`。您也可以在System属性中提供JSON作为`spring.application.json`，如下所示:

    $ java -Dspring.application.json='{"name":"test"}' -jar myapp.jar

> 您也可以使用命令行参数来提供JSON，如下所示:

    java -jar myapp.jar --spring.application.json='{"name":"test"}'

> 您也可以将JSON作为JNDI变量提供，如下所示

    java:comp/env/spring.application.json

### 24.1配置随机值
`RandomValuePropertySource`可用于注入随机值(例如，注入秘钥或测试用例)。它可以产生int值、long值、uuid或string值，如下例所示:

```
my.secret=${random.value}
my.number=${random.int}
my.bignumber=${random.long}
my.uuid=${random.uuid}
my.number.less.than.ten=${random.int(10)}
my.number.in.range=${random.int[1024,65536]}
```
`The random.int* syntax is OPEN value (,max) CLOSE where the OPEN,CLOSE are any character and value,max are integers. If max is provided, then value is the minimum value and max is the maximum value (exclusive).`

### 24.2 访问命令行属性
默认情况下，`SpringApplication`可以将任何命令行选项参数转换为属性(即参数以`--`开头，比如`--server.port=9000`)并将其添加到Spring环境中。并将其添加Spring环境中，如上面所示，命令行属性总是优先于其他属性源。

如果你不想通过命令行添加属性到环境中，你可以使用`SpringApplication.setAddCommandLineProperties(false)`禁止他们


### 24.3 应用程序配置文件
`SpringApplication`从以下环境加载配置文件`application.properties`并将他们添加到Spring环境中：
- 当前目录下的`/config`子目录
- 当前目录
- 包下`/config`classpath
- 根classpath

这个列表根据优先权进行排序（优先权高的会覆盖优先权低的）

> 你也可以使用['.yml'](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-external-config-yaml)，一种像'.properties'的配置文件



如果你不喜欢将`application.properties`作为配置文件名，你可以通过指定`spring.config.name`环境属性切换到另一个文件名。你也可以使用`spring.config.name`指定明确的位置。这是目录位置或文件路径的逗号分隔列表)。以下例子演示如何指定不同的文件名:

    $ java -jar myproject.jar --spring.config.name=myproject


以下示例演示如何指定两个位置:

    $ java -jar myproject.jar --spring.config.location=classpath:/default.properties,classpath:/override.properties

`spring.config.name`和`spring.config.location`很早就被用来确定必须加载哪些文件，因此它们必须被定义为环境属性(通常是OS环境变量、系统属性或命令行参数)。    

如果` spring.config.location `包含目录（而不是文件），他们应该以`/`结尾（并且在运行时，在加载之前，附加上从spring.config.name生成的名称，包括特定于配置文件的文件名）`spring.config.location`中指定的文件按原样使用，不支持特定于配置文件的变体，并且被任何特定于配置文件的属性覆盖。

配置位置按相反顺序搜索。默认情况下，配置路径为：`classpath:/,classpath:/config/,file:./,file:./config/`那么搜索的结果排序为：
- `file: ./config/`
- `file: ./`
- `classpath:/config/`
- `classpath:/`

当使用`spring.config.location`配置自定义配置位置时,它们会替换默认的路径。例如，如果`spring.config.location`配置了该路径：`classpath:/custom-config/,file:./custom-config/`,s搜索顺序如下：
1. `file:./custom-config/`
2. `classpath:custom-config/`

或者当使用`spring.config.additional-location`配置自定以配置路径，除了默认位置之外，还会使用它们。在默认位置之前搜索其他位置。例如，如果配置了另外路径`classpath:/custom-config/,file:./custom-config/`，搜索顺序如下：

1. `file:./custom-config/`
2. `classpath:custom-config/`
3. `file:./config/`
4. `file:./`
5. `classpath:/config/`
6. `classpath:/`

这种搜索可以让你在一个配置文件中指定一个默认的值，并且他们会有选择的覆盖另一个值。你可以为你的应用程序添加一个默认的`application.propeties`（或者你用`spring.config.name`选择其它基本名字）在一个默认的路径下
。然后，可以在运行时用位于其中一个自定义位置的不同文件覆盖这些默认值。

> 如果你使用环境变量而不是系统变量，大多数操作系统不允许使用点来分离键名，但是你可以使用下划线代替（例如，`SPRING_CONFIG_NAME`代替`spring.config.name`）

> 如果你的应用在一个容器中运行，那么JNDI属性（在`java:comp/env`)或者servlet上下初始化参数可以用来代替，或者也可以使用环境变量或者系统变量来代替

### 24.4 指定配置文件
除了application.properties文件，特定的profile属性也可以使用如下约定命名：`application-{profile}.properties`。Environment有一个默认的配置文件（默认情况下，【default】），如果没有设置激活文件它们会被使用。换句话说，如果没有明确的激活文件，那么properties会从`application-default.properties`被加载。



指定的profile文件和标准的application.properties文件一样从同一个路径中被加载，特定的profile总是覆盖非特定的profile，无论特定profile是在jar中还是jar外。


如果指定了多个profile，则应用最后获取策略。例如，由spring.profiles.active属性指定的配置文件被添加到通过SpringApplication API配置的配置文件之后，因此优先。

如果你指定了任何文件在`spring.config.location`，那些特定的profile变体都不会被考虑。如果你还想使用特定profile的属性，请使用`system.config.location`中的目录

### 24.5 属性中的占位符
application.properties中的值通过现有的环境进行过滤，所以你可以引用前面定义过的值（例如，从系统属性）
```
app.name=MyApp
app.description=${app.name} is a Spring Boot application
```

> 你也可以该技术在现有的SpringBoot属性创建“短”变体，更多细节参考[77.4小节 使用短命令行参数](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#howto-use-short-command-line-arguments)

### 24.6 属性加密
SpringBoot不提供任何加密属性值的支持，然而，在Spring环境中提供了包含修改值所需的钩子点。`EnvironmentPostProcessor`接口允许你在应用程序启动之前操作，[76.3小节查看自定义启动环境上下文](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#howto-customize-the-environment-or-application-context)查看更多细节

如果你正在寻找一个安全的方法来存储证书和密码，[Spring Cloud Vault](https://cloud.spring.io/spring-cloud-vault/)项目提供了支持外部配置的[HashiCorp Vault](https://www.vaultproject.io/)

### 24.7 使用YAML代替Properties文件

[YAML](http://yaml.org/)是JSON和的超集，指定分成配置数据便利的格式。SpringApplication类会自动支持YAML替代属性，只要在你的类路径上有[Snake YAML](http://www.snakeyaml.org/)

> 如果你使用“Starters”，Snake YAML有`spring-boot-starter`自动提供

#### 24.7.1
Spring框架提供了两个很方便的类来帮你加载YAML文档。`YamlMapFactoryBean`将YAML加载为`Properties`，`YamlMapFactoryBean`将YAML加载为一个Map。

例如，考虑以下YAML文档:
```
environments:
	dev:
		url: http://dev.example.com
		name: Developer Setup
	prod:
		url: http://another.example.com
		name: My Cool App
```

上面案例中可以转换为properties：
```
environments.dev.url=http://dev.example.com
environments.dev.name=Developer Setup
environments.prod.url=http://another.example.com
environments.prod.name=My Cool App
```

YAML lists are represented as property keys with [index] dereferencers. For example, consider the following YAML:
```
my:
servers:
	- dev.example.com
	- another.example.com
```
上面案例中可以转换为properties：
```
my.servers[0]=dev.example.com
my.servers[1]=another.example.com
```
像SpringBoot中的`Binder`一样绑定属性值(这些正式`@ConfigurationProperties`要做的)，你需要在`java.util.List`（或`Set`）类型的目标bean中有一个属性值，并且你需要提供一个setter方法或者一个可变的值初始化它。例如，以下示例绑定到前面显示的属性:
```
@ConfigurationProperties(prefix="my")
public class Config {

	private List<String> servers = new ArrayList<String>();

	public List<String> getServers() {
		return this.servers;
	}
}
```

### 24.7.2 在Spring环境中暴露YAML属性
`YamlPropertySourceLoader`类可用于在Spring环境中将YAML公开为`PropertySource`,这样做可以让您使用`@Value`注释和占位符语法来访问YAML属性


# 24.7.3 多个profile YAML文件
您可以使用`spring.profiles`作为key在单个文件中指定多个特定于配置文件的YAML文档，来指示文档何时应用，如下例所示:
```
server:
	address: 192.168.1.100
---
spring:
	profiles: development
server:
	address: 127.0.0.1
---
spring:
	profiles: production & eu-central
server:
	address: 192.168.1.120
```
在上例中，如果`devlopment`profile文件被使用，`server.address`属性为`127.0.0.1`。类似的，如果`production`和`eu-central`文件被使用，，`server.address`属性为`192.168.1.120`。如果`development`, `production` 和` eu-central`都没使用，那么这个值是`192.168,1.100`

> 因此，`spring.profiles`可以包含简单的profile名称（例如`producton`）或者是一个profile表达式。一个profile表达式允许表达更复杂的逻辑，例如`production & (eu-central | eu-west)` [查看参考指南](https://docs.spring.io/spring/docs/5.1.5.RELEASE/spring-framework-reference/core.html#beans-definition-profiles-java)了解更多细节。

当应用上下文没有明确的被激活，默认profile会被激活。因此，在下面这个YAML中，我们为`spring.security.user.password`设置了一个值，该值仅在“默认”配置文件中可用:
```
server:
  port: 8000
---
spring:
  profiles: default
  security:
    user:
      password: weak
```
然而，在下面的示例中，密码总是被设置的，因为它没有附加到任何配置文件，并且根据需要，它必须在所有其他配置文件中被显式重置:
```
server:
  port: 8000
spring:
  security:
    user:
      password: weak
```
使用Spring profile指定`spring.profiles`元素可以使用`!`字符。如果为单个文件指定了否定和非否定profile，至少要匹配一个非否定profile，并且没有否定的profile会匹配

### 24.7.4 YAML 缺点
YAML文件不可以使用`@PropertySource`注解，因此，在这个案例中你需要使用另一种方式加载属性值，你需要使用properties文件。

使用多个YAML文旦语法在特定于配置文件的YAML文件中使用多YAML文档语法会导致意外行为。例如，考虑一个名为`application-dev.yml`的文件以下配置，其中dev配置文件被激活
```
server:
  port: 8000
---
spring:
  profiles: !test
  security:
    user:
      password: weak
```
在上面的例子中，profile否定和profile表达式的行为不像预期的那样。我们建议您不要组合特定于配置文件的YAML文件和多个YAML文档，而只使用其中一个。

### 24.8 类型安全配置属性
使用`@Value("${property}")`注解注入配置属性有时是很笨重的，尤其是当你使用多个属性或者你的数据是分层时。SpringBoot提供了另一种处理属性的方法，让强类型beans管理和验证应用程序的配置，如下所示：
```
package com.example;

import java.net.InetAddress;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

import org.springframework.boot.context.properties.ConfigurationProperties;

@ConfigurationProperties("acme")
public class AcmeProperties {

	private boolean enabled;

	private InetAddress remoteAddress;

	private final Security security = new Security();

	public boolean isEnabled() { ... }

	public void setEnabled(boolean enabled) { ... }

	public InetAddress getRemoteAddress() { ... }

	public void setRemoteAddress(InetAddress remoteAddress) { ... }

	public Security getSecurity() { ... }

	public static class Security {

		private String username;

		private String password;

		private List<String> roles = new ArrayList<>(Collections.singleton("USER"));

		public String getUsername() { ... }

		public void setUsername(String username) { ... }

		public String getPassword() { ... }

		public void setPassword(String password) { ... }

		public List<String> getRoles() { ... }

		public void setRoles(List<String> roles) { ... }

	}
}
```
如上所示，该POJO定义了以下属性：
- `acme.enabled`默认值是false
- `acme.remote-address`，该类型可以强制从String中获得
- `acme.security.username`，嵌套的“security”对象，其名称由属性的名称确定。特别是，返回类型在那里根本没有使用，可能是SecurityProperties。
- `acme.security.password`
- `acme.security.roles`一组字符串

> Getters和Setters通常是必须的，因为绑定是通过标砖的Java Bean属性描述的，比如Spring MVC，setter可能在以下情况中省略：

- Map，只要他们被初始化，需要一个getter但不需要setter，因为他们可以由绑定改变
- 集合和数组都可以通过下标来访问（典型的YAML）或者使用一个逗号分隔属性值。在后面的例子中，setter是强制性的，我们推荐你总是为类型添加setter，如果你是初始化一个集合，请确保它是不可变的（如上例子所示）
- 如果嵌入的POJO属性被初始化（如前面的Security字段），setter则是不需要。如果你想要绑定者使用默认构造器创建一个实例，那么你需要setter

> 有些人使用Lombok自动生成getter和setter。确保Lombok不会为这种类型生成任何特定的构造函数，因为容器会用它来实例化对象。最后，只考虑标准Java Bean属性，不支持静态属性绑定。

> 也可以查看`@Value`和`@ConfigurationProperties`之间的区别。

你也需要列出在`@EnableConfigurationProperties`注解上要注册的类，如下所示：
```
@Configuration
@EnableConfigurationProperties(AcmeProperties.class)
public class MyConfiguration {
}
```

当`@ConfigurationProperties` bean以这种方式注册时，这个bean具有常规名称:`<prefix>-<fqn>`， 其中，`<prefix>`是`@ConfigurationProperties`注解中指定的环境密钥前缀，`<fqn>`是bean的完全限定名称。如果注释没有提供任何前缀，则只使用bean的完全限定名称。 上例中的bean名称是`acme-com.example.AcmeProperties`

即使前面的配置为`AcmeProperties`创建了一个常规bean，我们还是建议`@ConfigurationProperties`只处理环境，尤其是不从上下文中注入其他bean，话虽如此，`@EnableConfigurationProperties`注释也会自动应用到您的项目中，以便从环境中配置任何用`@ConfigurationProperties`注释的现有bean。你可以通过`AcmeProperties`来确定`MyConfiguration`已经是一个bean，如下所示：
```
@Component
@ConfigurationProperties(prefix="acme")
public class AcmeProperties {

	// ... see the preceding example

}
```

这种配置方式在SpringApplication外部YAML配置中特别适用，如下所示:
```
# application.yml

acme:
	remote-address: 192.168.1.1
	security:
		username: admin
		roles:
		  - USER
		  - ADMIN

# additional configuration as required
```
使用@ ConfigurationProperties beans，你可以用同样的方式注入任何备bean中，如下所示:
```
@Service
public class MyService {

	private final AcmeProperties properties;

	@Autowired
	public MyService(AcmeProperties properties) {
	    this.properties = properties;
	}

 	//...

	@PostConstruct
	public void openConnection() {
		Server server = new Server(this.properties.getRemoteAddress());
		// ...
	}

}
```

使用`@ConfigurationProperties`还可以生成元数据文件，IDEs可以使用这些文件自动完成自己的密钥。有关信息，请参考[附录B，配置元数据](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#configuration-metadata)

#### 24.8.1 第三方配置

除了在一个类上使用`@ConfigurationProperties`注解，你也可以在一个公共方法上使用`@Bean`。当你想在不受控制的第三方组件上绑定属性，这样是极其有效的。

从环境变量属性中配置一个bean，使用`@ConfigurationProperties`注册一个bean，如下所示：
```
@ConfigurationProperties(prefix = "another")
@Bean
public AnotherComponent anotherComponent() {
	...
}
```
任何以`another`为前缀的属性都会映射到`AnotherComponent`Bean中，类似于前面的`AcmeProperties`例子。

#### 24.8.2 宽松绑定
SpringBoot使用宽松的规则将属性绑定到`@ConfigurationProperties beans`中，所以环境属性和bean属性之间的名称不需要明确指定。常见的例子有“-”来分隔（例如，`context-path` 绑定到`contextPath`）和大小写环境属性（例如，`PORT`绑定到`port`）

例如，参考以下@ConfigurationProperties类：
```
@ConfigurationProperties(prefix="acme.my-project.person")
public class OwnerProperties {

	private String firstName;

	public String getFirstName() {
		return this.firstName;
	}

	public void setFirstName(String firstName) {
		this.firstName = firstName;
	}

}
```
在上一案例中，可以使用以下属性名：

##### 表24.1.宽松绑定

| 属性                                | 注意                                                  |
| ----------------------------------- | ----------------------------------------------------- |
| `acme.my-project.person.first-name` | 短横行情况，建议使用`.properties`和`.yml`文件         |
| `acme.myProject.person.firstName`   | 标准的驼峰语法                                        |
| `acme.my_project.person.first_name` | 下划线表示，这是使用`.properties`和`.yml`的另一种格式 |
| `ACME_MYPROJECT_PERSON_FIRSTNAME`   | 大写格式，当使用系统环境变量时推荐使用                |

注解的前缀值一定要短横线（小写并且短“-”分隔，例如`acme.my-project.person`）

##### 表24.2 每个属性源的宽松绑定规则

| 属性源         | 简介                                                | 列表                                                         |
| -------------- | --------------------------------------------------- | ------------------------------------------------------------ |
| properties文件 | 驼峰，短横线或下划线                                | 标准的语法使用`[]`或逗号                                     |
| yml文件        | 驼峰，短横线或下划线                                | 标准的语法是逗号分隔或yml语法列表                            |
| 环境变量       | 以下划线为分隔符的大写格式，`_`不能在属性名称中使用 | 被下划线包围的数字，例如`MY_ACME_1_OTHER = my.acme[1].other` |
| 系统属性       | 驼峰，短横线或下划线                                | 标准的语法使用`[]`或逗号                                     |

> 我们属性值尽可能的以小写形式存储，例如`my.property-name=acme`

当绑定map属性时，如果是小写数字字母字符以外内容，如果没有被`[]`包围，则删除除字母数字短横线外的字符。例如，将下面的属性保存到map中
```
acme:
  map:
    "[/key1]": value1
    "[/key2]": value2
    /key3: value3
```
上面的属性绑定到map中，其中`/key1`,`/key2`和`/key3`作为map的键

24.8.3 复杂类型合并
当列表配置在多个位置时，通过替换整个列表来覆盖列表。

例如，假设MyPojo对象的名称和描述属性默认为null。以下示例公开了AcmeProperties中MyPojo对象的列表:
```
@ConfigurationProperties("acme")
public class AcmeProperties {

	private final List<MyPojo> list = new ArrayList<>();

	public List<MyPojo> getList() {
		return this.list;
	}

}
```
考虑以下配置：
```
acme:
  list:
    - name: my name
      description: my description
---
spring:
  profiles: dev
acme:
  list:
    - name: my another name
```
如果`dev` profile文件没有激活，`AcmeProperties.list`包含一个MyPojo条目，如前面所述。然而，如果启用了`dev` profile文件，列表仍然只包含一个条目(name是`my another name`,description是null)。此配置不会将第二个MyPojo实例添加到列表中，也不会合并这些项目。
当一个列表有多个profile文件，将使用优先级最高的配置文件(并且仅使用该配置文件)。参考以下配置：
```
acme:
  list:
    - name: my name
      description: my description
    - name: another name
      description: another description
---
spring:
  profiles: dev
acme:
  list:
    - name: my another name
```
在前面的示例中，如果`dev` profile文件处于活动状态，`AcmeProperties.list`包含一个MyPojo条目(name是`my another name`,description是null)。对于YAML，逗号分隔列表和YAML列表都可以用于完全覆盖列表的内容。
对于Map属性，可以与从多个来源绘制的属性值绑定。但是，对于多个源中的同一属性，将使用优先级最高的属性。以下示例公开了来自`AcmeProperties`的Map :
```
@ConfigurationProperties("acme")
public class AcmeProperties {

	private final Map<String, MyPojo> map = new HashMap<>();

	public Map<String, MyPojo> getMap() {
		return this.map;
	}

}
```
参考以下配置：
```
acme:
  map:
    key1:
      name: my name 1
      description: my description 1
---
spring:
  profiles: dev
acme:
  map:
    key1:
      name: dev name 1
    key2:
      name: dev name 2
      description: dev description 2
```
如果`dev` profile文件未激活，`AcmeProperties.map`包含一个`key`为`key1`的条目(name是`my name 1`,description是`my description 1 ` )。但是，如果启用了`dev` profile文件，map包含两个键值对key，一个是`key1` (name为`dev name 1`，description为`my description 1` )，另一个是key为key2 (name为`dev name 2`,description为`dev description 2` )。

> 前面的合并规则适用于所有properties的文件属性，而不是YAML文件。

### 24.8.4 属性转换
SpringBoot尝试将外部应用属性强制绑定到`@ConfigurationProperties`bean上，如果你需要自定义转换类型，你可以提供`ConversionService`bean（这个bean的名称是`conversionService`）或者编辑自定义属性（通过`CustomEditorConfigurer`bean）或者自定义`Converters`（这个bean被注解`@ConfigurationPropertiesBinding`定义） 

> 由于这个bean是在应用程序生命周期早期被请求的，确保限制你`ConversionService`使用的依赖关系。通常，你需要的任何依赖在创建的时候没有初始化。
> 如果配置key不需要强制自定义转换服务，并且仅依赖于用`@ConfigurationProperties Binding`限定的自定义转换器，则可能需要重命名自定义的`ConversionService`。

#### 持续转换时间

SpringBoot为表示持续时间做出了专门的支持。如果你公开了`java.time.Duration `属性，下面的这个应用属性格式是可用的：
- 正常`long`表示(除非指定了`@DurationUnit`，否则使用毫秒作为默认单位)
- `java.util.Duration`使用标准的ISO-8601格式
- 一种更易阅读的方式，其中值和单位在一起（例如：`10s`，表示10秒）

考虑以下案例：
```
@ConfigurationProperties("app.system")
public class AppSystemProperties {

	@DurationUnit(ChronoUnit.SECONDS)
	private Duration sessionTimeout = Duration.ofSeconds(30);

	private Duration readTimeout = Duration.ofMillis(1000);

	public Duration getSessionTimeout() {
		return this.sessionTimeout;
	}

	public void setSessionTimeout(Duration sessionTimeout) {
		this.sessionTimeout = sessionTimeout;
	}

	public Duration getReadTimeout() {
		return this.readTimeout;
	}

	public void setReadTimeout(Duration readTimeout) {
		this.readTimeout = readTimeout;
	}

}
```
要指定session超时时间是30秒，30，PT30S和30S都是等效的。读取超时时间是500毫秒，可以按以下形式指定一个：500，500ms，PT0.5S。

你也可以使用任何支持的单位：比如：
- ns，纳秒
- us，微秒
- ms，毫秒
- s，秒
- m，分钟
- h，时
- d，天

默认单位为毫秒，可以使用`@DurationUnit`覆盖，如上例所示。

> 如果你从先前的版本进行升级，而以前的版本是使用Long来表示持续时间的。如果切换的持续时间单位不是毫秒，需确保定义一个单位（可以使用`@DurationUnit`）。这样做提供了透明的升级操作，同时支持更丰富的格式。

#### 转换数据大小
Spring框架有个`DataSize`类型的值，它允许使用byte表示大小，如果你公开了`DataSize`属性，应用程序有以下格式可用：
- 常规long表示（除非使用了`DataSizeUnit`进行指定，否则默认单位是byte）
- 一种更易阅读的方式，值和单位是在一起的（例如：10MB就是10兆byte）

参考如下案例：
```
@ConfigurationProperties("app.io")
public class AppIoProperties {

	@DataSizeUnit(DataUnit.MEGABYTES)
	private DataSize bufferSize = DataSize.ofMegabytes(2);

	private DataSize sizeThreshold = DataSize.ofBytes(512);

	public DataSize getBufferSize() {
		return this.bufferSize;
	}

	public void setBufferSize(DataSize bufferSize) {
		this.bufferSize = bufferSize;
	}

	public DataSize getSizeThreshold() {
		return this.sizeThreshold;
	}

	public void setSizeThreshold(DataSize sizeThreshold) {
		this.sizeThreshold = sizeThreshold;
	}

}
```

指定缓冲区的大小为10兆字节，10和10MB是等效的。256字节的阀值可以用256或256B来指定。

你可以使用以下支持的单位表示，他们是：
- B，字节
- KB，千字节
- MB，兆字节
- GB，千兆字节
- TB，兆兆字节

默认单位是字节，可以使用@DataSizeUnit覆盖，如上例所示。

> 如果你从先前的版本进行升级，而以前的版本是使用Long来表示大小的。如果它不是字节，请确保在切换DataSize的同时需确保定义一个单位（可以使用`@DataSizeUnit`）。这样做提供了透明的升级操作，同时支持更丰富的格式。

### 24.8.5 确认`@ConfigurationProperties`
每当用Spring的`@Validated`注释对`@ConfigurationProperties`类进行注释时，Spring Boot都会尝试验证这些类。你可以使用JSR-303 中的`javax.vaildation`直接约束你的配置类。为此，请确保你的classpath兼容JSR-303并在字段上添加注解，如下所示：
```
@ConfigurationProperties(prefix="acme")
@Validated
public class AcmeProperties {

	@NotNull
	private InetAddress remoteAddress;

	// ... getters and setters

}
```
> 你还可以通过@Bean注解方法来触发验证，该方法使用@Validated创建配置属性。虽然嵌套属性在绑定时也会被验证，但是最好也将相关字段注释`为@Valid`。这确保即使没有找到嵌套属性，也能触发验证。以下示例基于前面的`AcmeProperties`示例:
```
@ConfigurationProperties(prefix="acme")
@Validated
public class AcmeProperties {

	@NotNull
	private InetAddress remoteAddress;

	@Valid
	private final Security security = new Security();

	// ... getters and setters

	public static class Security {

		@NotEmpty
		public String username;

		// ... getters and setters

	}

}
```

你可以创建一个自定义的bean名为`configurationPropertiesValidator`来自定义Spring`Validator`。@Bean方法应该声明为静态的。配置属性验证器是在应用程序生命周期的早期创建的，将@Bean方法声明为静态允许创建Bean，而不必实例化`@Configuration`类。这样做可以避免早期实例化可能导致的任何问题。有一个属性验证示例显示了如何设置。

> `spring-boot-actuator`模块包括一个暴露所有`@ ConfigurationProperties beans`的端点。将您的浏览器指向`/actuator/configprops`或使用等效的JMX端点。有关详细信息，请参见“[生产就绪功能](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#production-ready-endpoints)”部分。

### 24.8.6 @ConfigurationProperties 和 @Value的对比
`@Value`注释是核心容器功能，它不提供与类型安全配置属性相同的功能。下面这个表总结了@ConfigurationProperties和@Value支持的功能：

| 功能              | @ConfigurationProperties | @Value |
| ----------------- | ------------------------ | ------ |
| 宽松绑定          | yes                      | no     |
| 支持元数据        | yes                      | no     |
| `Spring EL`表达式 | no                       | yes    |

如果你为你的组件定义了一套配置键，我们建议你使用`@ConfigurationProperties`将它们分为一组。你应该知道，因为@Value不支持动态绑定，如果你需要通过使用环境变量来提供值，这不是一个好的选择。

最后，虽然您可以在`@Value`中编写一个Spring EL表达式，但是这些表达式不是从[应用程序属性文件](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-external-config-application-property-files)中处理的。

### 25. Profiles
Spring提供了一种方法来隔离应用程序配置的各个部分，并使其仅在某些环境中可用。任何`@Componet`或`@Configuragion`可以用`@Profile`标记，以限制其加载，如下所示：
```
@Configuration
@Profile("production")
public class ProductionConfiguration {

	// ...

}
```
你可以使用`spring.profiles.active Environment`属性指定那个profile被激活。你可以用本章前面描述的任何方式指定属性。例如，你可以使用`application.properties`包含它，如下所示：
```
spring.profiles.active=dev,hsqldb
```
你也可以在命令行中指定它，如下`--spring.profiles.active=dev,hsqldb`

### 25.1 添加有效的profile
`spring.profiles.active`属性有着和其它属性一样排序规则：最高的`PropertySource`获得。这就意味着你可以在`application.properties`指定profile并且可以使用命令行替换他们。

有时，将特定profile的属性添加到激活profile中而不是替换它们是很有用的。`spring.profiles.include`属性可以无条件添加有效的profile。SpringApplication入口点也有Java接口来添加profile文件，详情查看SpringApplication中的`setAdditionalProfiles()`方法。

例如，当应用程序使用如下属性进行运行--spring.profiles.active=prod`,那么` proddb`和 `prodmq`都会被激活
```
---
my.property: fromyamlfile
---
spring.profiles: prod
spring.profiles.include:
  - proddb
  - prodmq
```

> 请记住，`spring.profiles`属性可以在YAML文档中定义，以确定该特定文档何时包含在配置中。详见[第77.7节“根据环境更改配置”](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#howto-change-configuration-depending-on-the-environment)。

### 25.2 程序配置profile
你可以在应用程序运行前在代码中调用`SpringApplication.setAdditionalProfiles(…)`来激活profile。也可以使用Spring`ConfigurableEnvironment`接口来使用

---
2019-03-13 

### 25.3 指定profile配置文件
`application.properties`（或者`application.yml`）和`@ConfigurationProperties`都可以用于指定profile文件加载，详情参考[Section 24.4, “Profile-specific Properties](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-external-config-profile-specific-properties)了解更过细节。

### 26. 日志
SpringBoot使用[Commons Logging](https://commons.apache.org/logging)日志但是对底层实现保持开放。默认提供了[Java Util Loggin](https://docs.oracle.com/javase/8/docs/api//java/util/logging/package-summary.html)，[Log4J2](https://logging.apache.org/log4j/2.x/)和[Logback](http://logback.qos.ch/)配置。每种情况都提前配置了控制台输出功能也可以配置文件输出功能。

默认情况下如果使用“Starters”，会使用Logback进行记录。适当的Logback路由还包括Java Util Logging，Commons Logging，Log4j或者SLF4J依赖库，以确保他们能正常的工作。

> Java有很多的日志框架可以使用。如果对上述有疑惑不用担心。通常，你不需要改变你的日志依赖项，SpringBoot默认的日志就可以很好的工作了

### 26.1 日志格式
SpringBoot输出的日志如下所示：
```
2014-03-05 10:57:51.112  INFO 45469 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet Engine: Apache Tomcat/7.0.52
2014-03-05 10:57:51.253  INFO 45469 --- [ost-startStop-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2014-03-05 10:57:51.253  INFO 45469 --- [ost-startStop-1] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 1358 ms
2014-03-05 10:57:51.698  INFO 45469 --- [ost-startStop-1] o.s.b.c.e.ServletRegistrationBean        : Mapping servlet: 'dispatcherServlet' to [/]
2014-03-05 10:57:51.702  INFO 45469 --- [ost-startStop-1] o.s.b.c.embedded.FilterRegistrationBean  : Mapping filter: 'hiddenHttpMethodFilter' to: [/*]
```
会输出以下内容：
- 日期和时间：精确到毫秒且易于分类
- 日志等级：ERROR,WARN,INFO,DEBUG,或者TRACE
- 进程ID
- 一个`---`分隔符用来区分日志信息
- 线程名称：使用中括号扩起来
- 日志名称：通常是类的名称（一般会缩写）
- 日志信息
> Logback 没有FATAL等级，它被映射到ERROR中了

### 26.2 控制台输出
默认的日志配置会在写入消息的时候打印到控制台中。默认情况下会记录ERROR，WARN，INFO级别信息，你可以通过`--debug`标志来启动debug模式

```
java -jar myapp.jar --debug
```
你也可以在你的application.properties中指定`debug=true`

当debug模式被启动，一些核心日志（内嵌容器，Hibernate，SpringBoot）会被配置为输出更多的信息。启用debug模式不会将应用配置为具有debug级别的消息。

或者你可以通过运行应用加`--trace`标志或者在你的application.properties文件中添加trace=true来启动trace模式。这样做可以为核心日志(嵌入式容器、Hibernate和整个Spring产品组合)的选择启用跟踪日志记录。

---
2019-03-14

#### 26.2.1 输出有颜色的代码
如果你的终端支持ANSI，输出有颜色的代码有助于可读性。你可以设置spring.output.ansi.enabled支持的值来覆盖默认的值

颜色编码是通过%clr进行转换配置的。最简单的形式，转换器根据日志级别输出颜色，如下所示：
```
%clr(%5p)
```
下表描述来日志级别和颜色的对应：

| 级别  | 颜色 |
| ----- | ---- |
| FATAL | 红色 |
| ERROR | 红色 |
| WARN  | 黄色 |
| INFO  | 绿色 |
| DEBUG | 绿色 |
| TRACE | 绿色 |

或者，你可以通过颜色或样式为转换选项指定使用什么颜色或样式。例如，让文本显示黄色，使用以下配置：
```
%clr(%d{yyyy-MM-dd HH:mm:ss.SSS}){yellow}
```
支持以下颜色和样式：
- 蓝色
- 青色
- 微粉色
- 品红
- 红色
- 黄色

### 26.3 文件输出
默认情况下，Spring Boot支持把日志输出到控制台但不支持输出到日志文件中。如果你除了在控制台输出还要在文件中输出日志，你需要设置logging.file或logging.path属性（例如，在你的application.properties）

##### 表 26.1日志属性

| 日志文件 | 日志路径 | 案例     | 描述                                                         |
| -------- | -------- | -------- | ------------------------------------------------------------ |
| 无       | 无       |          | 只在控制台输出                                               |
| 指定文件 | 无       | my.log   | 指定到日志文件，路径名可以是相对位置，也可以是绝对位置       |
| 无       | 指定目录 | /var/log | 将spring.log写到指定的目录，路径名可以是相对位置，也可以是绝对位置 |

日志文件达到10MB就会分割，并且和控制台输出的一样，默认情况下会记录ERROR。WARN和INFO级别的信息。文件大小可以通过logging.file.max-size属性来修改。除非设置了logging.file.max-history属性，否则以前分割的文件将永久保存。

> 日志系统初始化发生在应用早期生命周期中，因此，使用@PropertySource注释加载属性文件中找不到日志属性参数

> 日志记录属性独立于实际的日志记录基础架构。因此，指定配置key（例如，为logback.configurationFile配置Logback）而不是由SpringBoot管理

---
2019-03-15


### 26.4 日志等级
在Spring中所有日志系统都支持日志等级（例如，在application.properties）中使用logging.level.<logger-name>=<level>的方式来设置，level可以是TRACE，DEBUG，INFO，WARN，ERROR，FATAL或OFF。root日志可以通过logging.level.root来配置

下面这个例子是在application.properties中设置
```
logging.level.root=WARN
logging.level.org.springframework.web=DEBUG
logging.level.org.hibernate=ERROR
```
### 26.5 日志组
能够将相关的记录器组合在一起，以便它们可以同时配置，这通常很有用。例如，您可能通常会更改所有与Tomcat相关的日志记录程序的日志记录级别，但是您不容易记住顶级包。

为了解决这个问题，SpringBoot允许你在Spring环境中定义日志组。例如，你可以在你的application.properties中添加tomcat组：
```
logging.group.tomcat=org.apache.catalina, org.apache.coyote, org.apache.tomcat
```
SpringBoot预定了一些日志组，你可以开箱即用：

| 名称 | 日志                                                         |
| ---- | ------------------------------------------------------------ |
| web  | org.springframework.core.codec, org.springframework.http, org.springframework.web |
| sql  | org.springframework.jdbc.core, org.hibernate.SQL             |


### 26.6 自定义日志配置
可以通过在类路径中包含适当的库来激活各种日志记录系统，并且可以通过在类路径的根或由以下Spring环境属性:logging.config指的位置提供适当的配置文件来进一步定制日志记录系统

你可以通过使用org.springframework.boot.logging.LoggingSystem 属性来指定SpringBoot的日志系统。该值应该LoggingSystem实现的完全限定类名。您也可以使用值none完全禁用弹簧引导的日志记录配置。

> 由于日志是在应用上下文创建之前初始化的，因此不能从Spring de @Configuration文件中的@PropertySource控制记录。更改或禁用日志系统的唯一方法就是通过设置系统属性。

依赖你的日志系统，以下文件会被加载：

| 日志系统名称             | 文件名                                                       |
| ------------------------ | ------------------------------------------------------------ |
| Logback                  | logback-spring.xml, logback-spring.groovy, logback.xml, or logback.groovy |
| Log4j2                   | log4j2-spring.xml或log4j2.xml                                |
| JDK（Java Util Logging） | logging.properties                                           |

> 如果可以的话，我们推荐你在日志文件配置中使用-spring（例如，logback-spring.xml，而不用logbook.xml）。如果你使用标准配置位置，Spring不能完全控制日志初始化


> 当从可执行jar运行时，Java Util日志加载时会引起一个问题。如果可能的话，我们建议你避免使用它。

为了帮助你实现自定义，一些其它属性从Spring环境转移系统属性下，如下所示：

| Spring 环境                       | 系统属性                      | 注释                                                         |
| --------------------------------- | ----------------------------- | ------------------------------------------------------------ |
| logging.exception-conversion-word | LOG_EXCEPTION_CONVERSION_WORD | 当日志异常时会进行转换                                       |
| logging.file                      | LOG_FILE                      | 如果定义了就使用默认的配置                                   |
| logging.file.max-size             | LOG_FILE_MAX_SIZE             | 日志文件最大值（如果启动了LOG_FILE）（只支持默认的Logback）  |
| logging.file.max-history          | LOG_FILE_MAX_HISTORY          | 日志文件最长保留时间，（如果启动了LOG_FILE）（只支持默认的Logback） |
| logging.path                      | LOG_PATH                      | 如果定义了，会使用默认的日志配置                             |
| logging.pattern.console           | CONSOLE_LOG_PATTERN           | 该模式在控制台输出日志（只支持默认的Logback）                |
| logging.pattern.dateformat        | LOG_DATEFORMAT_PATTERN        | 日志日期格式追加模式（只支持默认的Logback）                  |
| logging.pattern.file              | FILE_LOG_PATTERN              | 在文件中使用该日志模式（只支持默认的Logback）                |
| logging.pattern.level             | LOG_LEVEL_PATTERN             | 这个这个格式这个格式用来这个格式用来呈现 这个格式用来呈现日志这个格式用来呈现日志级别（默认%5p）。（只支持默认的Logback） |
| PID                               | PID                           | 当前进程ID                                                   |

解析配置文件时，所有支持的日志记录系统都可以参考系统属性。有关示例，请参见spring-boot.jar中的默认配置:
- [LogBack](https://github.com/spring-projects/spring-boot/tree/v2.1.3.RELEASE/spring-boot-project/spring-boot/src/main/resources/org/springframework/boot/logging/logback/defaults.xml)
- [Log4j2](https://github.com/spring-projects/spring-boot/tree/v2.1.3.RELEASE/spring-boot-project/spring-boot/src/main/resources/org/springframework/boot/logging/log4j2/log4j2.xml)
- [Java Util logging](https://github.com/spring-projects/spring-boot/tree/v2.1.3.RELEASE/spring-boot-project/spring-boot/src/main/resources/org/springframework/boot/logging/java/logging-file.properties)

> 如果你想再日志属性中使用占位符，你应该使用SpringBoot语法不是框架的语法。值得注意的是，如果你使用LogBack，你应该使用":"作为属性名和默认值之间的分隔符，而不是":-"

> 你可以通过覆盖LOG_LEVEL_PATTERN (或Logback中logging.pattern.level)来将MDC和其他特殊内容添加到日志行中。例如，如果您使用logging.pattern . evel=user:%X { user } %5p，则默认日志格式包含“user”的MDC条目(如果存在)，如下例所示:


    2015-09-30 12:30:04.031 user:someone INFO 22174 --- [  nio-8080-exec-0] demo.Controller
    Handling authenticated request

---
2019-03-16

### 26.7 扩展日志
SpringBoot包含了一系列扩展来帮助你使用Logback高级配置。你可以在你的logback-spring.xml中配置文件使用这些扩展。

> 由于标准的logback.xml文件过早的被加载，你不能在文件中使用扩展。你可以在logback-spring.xml或在logging.config属性中进行定义。

> 这些扩展不能使用Logback配置扫描。如果你尝试这么做的话，对配置文件进行修改会导致类似以下错误
```
ERROR in ch.qos.logback.core.joran.spi.Interpreter@4:71 - no applicable action for [springProperty], current ElementPath is [[configuration][springProperty]]
ERROR in ch.qos.logback.core.joran.spi.Interpreter@4:71 - no applicable action for [springProfile], current ElementPath is [[configuration][springProfile]]
```

### 26.7.1 指定profile配置文件
在可用的Spring profile中，<springProfile>标签可以让你有选择性的包含或排除配置部分。Profile节点支持<configuration>元素中的任何位置。使用name属性指定哪个profile接受配置。<springProfile>可以接受一个简单的profile名次（例如，staging）或者profile表达式。一个profile表达式允许更复杂的profile逻辑，例如，`production & （eu-central | eu-west).`查看[参考手册](https://docs.spring.io/spring/docs/5.1.5.RELEASE/spring-framework-reference/core.html#beans-definition-profiles-java)了解更多细节.以下列表展示了3个简单的profile文件：
```
<springProfile name="staging">
	<!-- configuration to be enabled when the "staging" profile is active -->
</springProfile>

<springProfile name="dev | staging">
	<!-- configuration to be enabled when the "dev" or "staging" profiles are active -->
</springProfile>

<springProfile name="!production">
	<!-- configuration to be enabled when the "production" profile is not active -->
</springProfile>
```
### 26.7.2 环境属性
<springProperty>标签可以让你在Logback中使用Spring环境暴露出来的属性。如果你需要在你的application.properties文件中访问值，那么这对你是有帮助的。这个标签和其它标准的属性标签一样的工作方式。但是，不是直接指定一个值，而是指定这个属性源（来自环境变量）如果需要存储属性到本地范围之外，你可以使用scope属性，如果你需要返回值（在环境变量为设置的情况下）。你可以使用defaultValue属性。
以下案例显示了在Logback中如何暴露属性
```
<springProperty scope="context" name="fluentHost" source="myapp.fluentd.host"
		defaultValue="localhost"/>
<appender name="FLUENT" class="ch.qos.logback.more.appenders.DataFluentAppender">
	<remoteHost>${fluentHost}</remoteHost>
	...
</appender>
```

> 这个源必须使用“-”模式进行指定（比如：my.property-name)。然而，属性可以通过宽松的规则添加到环境中

---
2019-03-17

27. 国际化
   SpringBoot支持本地消息所以你可以创建你喜欢的语言。默认下，SpringBoot会在消息源根类路径下查找

> 当默认的属性文件资源包是可用的自动配置就会去使用（默认是messags.properties）如果你的资源包指定了一个属性文件，你需要添加默认值

资源包基本名称和其它一些属性可以使用spring.message命名空间来配置，如下所示：
```
spring.messages.basename=messages,config.i18n.messages
spring.messages.fallback-to-system-locale=false
```

spring.messages.basename支持逗号分隔的位置列表，可以是包名，也可以是从类路径根解析的资源。

查看[MessageSourceProperties ](https://github.com/spring-projects/spring-boot/tree/v2.1.3.RELEASE/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/context/MessageSourceProperties.java)了解更多支持选项



### 28. JSON
SpringBoot集成了3个JSON映射库：
- Gson
- Jackson
- JSON-B

Jackson是首选也是默认的库

### 28.1 Jackson
Jackson提供了自动装配，并且Jackson是spring-boot-starter-json的一部分。当Jackson在类路径ObjectMapper Bean就会自动装配
。为自定义配置的提供了几个不同的配置属性

### 28.2 Gson
Gson提供了自动装配，当Gson在类路径上会被自动配置。为自定义配置提供了几个不同的spring.gson.*配置属性。要使用更多，可以使用GsonBuilderCustomizer

### 28.3 JSON-B
JSON-B提供了自动装配，当JSON-B API被实现的时候，将在类路径上自动配置一个Jsonb bean。首选的JSON-B实现是Apache Johnzon，它提供了依赖性管理。

### 29 开发Web应用

SpringBoot非常适合Web应用开发，你可以使用内嵌的Tomcat，Jetty, Undertow或 Netty.创建一个HTTP服务，
大部分Web应用使用spring-boot-starter-web模块就可以快速运行。你也可以选择spring-boot-starter-webflux模块构建一个响应式Web应用。

如果你还没有使用SpringBoot开发过Web应用，你可以在[快速开始](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#getting-started-first-application)章节从案例Hello World开始

### 29.1 Spring Web MVC 框架
[Spring Web MVC Framework](https://docs.spring.io/spring/docs/5.1.5.RELEASE/spring-framework-reference/web.html#mvc)经常也简称为Spring MVC，是一个丰富的模型视图控制器Web框架。Spring MVC可以让你使用@Controller和@RestController来指定处理HTTP请求，你controller中的方法通过使用@RequestMapping来映射HTTP请求。

下面代码演示了一个典型的@RestController，该服务提供JSON数据：
```
@RestController
@RequestMapping(value="/users")
public class MyRestController {

	@RequestMapping(value="/{user}", method=RequestMethod.GET)
	public User getUser(@PathVariable Long user) {
		// ...
	}

	@RequestMapping(value="/{user}/customers", method=RequestMethod.GET)
	List<Customer> getUserCustomers(@PathVariable Long user) {
		// ...
	}

	@RequestMapping(value="/{user}", method=RequestMethod.DELETE)
	public User deleteUser(@PathVariable Long user) {
		// ...
	}

}
```
Spring MVC是Spring框架的一部分，更多细节查看[文档](https://docs.spring.io/spring/docs/5.1.5.RELEASE/spring-framework-reference/web.html#mvc)，在[spring.io/guides](https://spring.io/guides)中也提供了一些有价值的手册

---
2019-03-18

### 29.1.1 Spring MVC 自动装配

springBoot为Spring MVC提供了自动装配，以适用于更多的应用程序。

自动装配在Spring默认功能上添加了以下功能：
- 包含了ContentNegotiatingViewResolver和BeanNameViewResolverbean
- 支持静态资源服务，包括对WebJar的支持（[本文稍后讨论](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-spring-mvc-static-content)）
- 支持HttpMessageConverters（[本文稍后讨论](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-spring-mvc-static-content)）
- 自动注册MessageCodesResolver（[本文稍后讨论](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-spring-message-codes)）
- 支持静态index.html
- 支持自定义Favicon（[本文稍后讨论](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-spring-mvc-favicon)）
- 自动使用ConfigurableWebBindingInitializer bean（[本文稍后讨论](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-spring-mvc-web-binding-initializer)）

如果你需要保留SpringBoot MVC的功能你需要另外添加MVC配置（拦截器，格式转化，视图控制器，和其它的一些特性），你可以在你自己的WebMvcConfigurer类上添加@Configuration,但不能与欧@EnableWebMvc。如果你希望提供RequestMappingHandlerMapping, RequestMappingHandlerAdapter, 或 ExceptionHandlerExceptionResolver, 你要声明一个 WebMvcRegistrationsAdapter实例来提供这样的组件。

如果你想要完全控制Spring MVC，你要在你的@EnableWebMvc上添加@Configuration注解

### 29.1.2 Http消息转换
Spring MVC使用HttpMessageConverter接口进行转换HTTP的请求和响应。例如，对象会自动转换成JSON（使用Jackson库）或者XML（如果可以的话，使用扩展的Jackson，否则使用JAXB，默认情况字符串使用utf-8

如果你想要添加自定义转换器，你可以使用SpringBoot HttpMessage类，如下清单所示：
```
import org.springframework.boot.autoconfigure.web.HttpMessageConverters;
import org.springframework.context.annotation.*;
import org.springframework.http.converter.*;

@Configuration
public class MyConfiguration {

	@Bean
	public HttpMessageConverters customConverters() {
		HttpMessageConverter<?> additional = ...
		HttpMessageConverter<?> another = ...
		return new HttpMessageConverters(additional, another);
	}

}

```
上下文中存在的任何HttpMessageConverter都会添加到转换器的list中。你也可以用同样的方式覆盖默认转换器

### 29.1.3 自定义JSON序列化和反序列化
如果你使用Jackson进行JSON的序列化和反序列化，你需要自己写一个JsonSerializer和JsonDeserializer类。自定义序列化类通常使用Jackson模块，但是SpringBoot另外提供了@JsonComponent注解更方便可以直接注册到bean上

你可以在JsonSerializer和JsonDeserializer实现类上直接使用@JsonComponent注解。你也可以在泪中包含序列化和反序列话的内部类，如下所示：
```
import java.io.*;
import com.fasterxml.jackson.core.*;
import com.fasterxml.jackson.databind.*;
import org.springframework.boot.jackson.*;

@JsonComponent
public class Example {

	public static class Serializer extends JsonSerializer<SomeObject> {
		// ...
	}

	public static class Deserializer extends JsonDeserializer<SomeObject> {
		// ...
	}

}

```
在应用程序的上下文中所有的@JsonComponent标注的bean都会被Jackson自动注册。因为@JsonComponent是@Component的原注解，会被组件扫描规则应用到

Spring Boot还提供了JsonObjectSerializer和JSonobjectSerializer基类，它们在序列化对象时为标准Jackson版本提供了有用的替代方法。有关于这两个的细节参考文档

---
2019-03-19
### 29.1.4 消息码解析器
Spring MVC 有一个生成错误代码的策略，用来呈现来自绑定错误的粗我消息：消息码解析器。如果你设置了spring.mvc.message-codes-resolver.format属性为PREFIX_ERROR_CODE或POSTFIX_ERROR_CODE，SpringBoot会为你创建一个（查看[DefaultMessageCodesResolver.Format](https://docs.spring.io/spring/docs/5.1.5.RELEASE/javadoc-api/org/springframework/validation/DefaultMessageCodesResolver.Format.html)枚举）

#### 静态内容

默认下，SpringBoot服务的静态内容来自类路径上一个叫做/static或者/punlic或者/resources或者/META-INF/resources的目录或者来自根的ServletContext。这个用的是Spring MVC的ResourceHttpRequestHandler，因此你可以添加WebMvcConfigurer来覆盖addResourceHandlers方法来修改具体行为。

在一个标准独立的web应用中，容器中默认的Servlet会被启动充当后备，如果Spring决定不处理内容，则由ServletContext提供内容。大多数情况下这不会发生，除非你修改了默认的MVC配置，因为Spring总是通过DispatcherServlet来处理请求的。

默认情况下，资源会映射到/**上，但是你可以通过spring.mvc.static-path-pattern属性设置。例如，将所有的资源移动到/resources/**下，可以这样做：
```
spring.mvc.static-path-pattern=/resources/**
```

你也可以通过spring.resources.static-locations属性自定义静态资源位置（代替列表中的默认值）。根Servlet上下文路径/也会自动的添加一个位置。

除了前面提到的“标准”静态资源位置之外，针对WebJar内容制作了一个特例，任何在/ webjars / * *中有路径的资源，如果以Webjar格式打包，则从jar文件中提供。

> 如果你的应用程序要打包成jar，请不要使用src/main/webapp目录。尽管这个目录是一个通用标准，但它只工作于war包，如果生成一个jar，它会被大多数构建工具所忽略。


让缓存失效，以下配置为所有静态资源配置了缓存失效解决方案，有效地添加内容散列，例如在URL上`<link href="/css/spring-2a2d595e6ed9a0b24f027f2b63b134d6.css"/>`
```
spring.resources.chain.strategy.content.enabled=true
spring.resources.chain.strategy.content.paths=/**
```

> 在运行时链接资源会重写到模版上，感谢ResourceUrlEncodingFilter为Thymeleaf和FreeMarker自动配置了。但当你使用JSP时，应该手动声明此过滤器目当前不自动支持其他的模板引擎，但可以使用自定义模板宏/助手和[ResourceUrlProvider](https://docs.spring.io/spring/docs/5.1.5.RELEASE/javadoc-api/org/springframework/web/servlet/resource/ResourceUrlProvider.html).

当动态加载资源时，例如，加载js模块，重命名文件不是一个好的选择。这就是为什么其他策略也得到支持并可以结合起来。一个“fix”策略在url上添加静态版本字符串而不需要改变文件名，如下所示：
```
spring.resources.chain.strategy.content.enabled=true
spring.resources.chain.strategy.content.paths=/**
```
```
spring.resources.chain.strategy.fixed.enabled=true
spring.resources.chain.strategy.fixed.paths=/js/lib/
spring.resources.chain.strategy.fixed.version=v12
```

通过这种配置，位于/js/lib下的Js模块使用固定版本策略(/v12/js/lib/mymodule.js)来进行控制,而其他的资源仍然使用这种方式（<link href="/css/spring-2a2d595e6ed9a0b24f027f2b63b134d6.css"/>）

更多支持的选项，请参见[ResourceProperties](https://github.com/spring-projects/spring-boot/tree/v2.1.3.RELEASE/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/web/ResourceProperties.java)。

> 这个特性已经在一篇[博客](https://spring.io/blog/2014/07/24/spring-framework-4-1-handling-static-web-resources)和Spring Framework的[参考文档](https://docs.spring.io/spring/docs/5.1.5.RELEASE/spring-framework-reference/web.html#mvc-config-static-resources)中专门做了详细的描述。

---
2019-03-20

### 29.1.6 欢迎页面
SpringBoot支持静态和模版欢迎页。在本地静态配置文件中会先去查找index.html文件。如果没有找到，他会去找index模版。如果找到一个，会自动应用为应用程序的欢迎页面。

### 29.1.7 自定义Favicon
SpringBoot会先从静态配置文件中再去根路径查找查找favicon.ico。如果这个文件存在，会自动使用为应用的favicon

### 29.1.8 路径匹配和内容协商
Spring MVC可以通过查看请求路径并将其与应用程序中定义的进行映射匹配，将传入的HTTP请求映射到处理程序(例如，Controller方法上的@GetMapping注解)。

SpringBoot默认下禁止选用后缀匹配，这就意味着类似"GET /projects/spring-boot.json"不会匹配到@GetMapping("/projects/spring-boot") 上。这被认为是Spring MVC应用程序的最佳实践。这个特性在过去对于没有发送正确的“Accept”请求头的HTTP客户端非常有用；我们需要确保向客户端发送正确的内容类型。如今，内容协商更加可靠。

对于发送“Accept”属性不一致的HTTP请求头还有另一种处理方法。使用后缀匹配代替，我们可以使用查询参数来确保类似"GET /projects/spring-boot?format=json"的请求会映射到@GetMapping("/projects/spring-boot")
```
spring.mvc.contentnegotiation.favor-parameter=true

# We can change the parameter name, which is "format" by default:
# spring.mvc.contentnegotiation.parameter-name=myparam

# We can also register additional file extensions/media types with:
spring.mvc.contentnegotiation.media-types.markdown=text/markdown
```
如果您理解这些警告，并且仍然希望您的应用程序使用后缀模式匹配，则需要以下配置:

```
spring.mvc.contentnegotiation.favor-path-extension=true
spring.mvc.pathmatch.use-suffix-pattern=true
```

或者，与其打开所有后缀模式，不如只支持注册后缀模式更安全:
```
spring.mvc.contentnegotiation.favor-path-extension=true
spring.mvc.pathmatch.use-registered-suffix-pattern=true

# You can also register additional file extensions/media types with:
# spring.mvc.contentnegotiation.media-types.adoc=text/asciidoc
```

### 29.1.9 ConfigurableWebBindingInitializer
Spring MVC使用webBindingInitializer来为一个特定的请求初始化WebDataBinder。SpringBoot会自动配置Spring MVC去使用它

### 29.1.10 模版引擎
除了REST Web服务，你还可以使用Spring MVC来动态的响应HTML内容。Spring MVC提供多种模版引擎技术，包括Thymeleaf, FreeMarker, 和 JSP。此外，还有许多其他模板引擎包括它们自己的Spring MVC集成。

SpringBoot 包括对以下模板引擎的自动装配支持：
- [FreeMarker](https://freemarker.apache.org/docs/)
- [Groovy](http://docs.groovy-lang.org/docs/next/html/documentation/template-engines.html#_the_markuptemplateengine)
- [Thymeleaf](http://www.thymeleaf.org/)
- [Mustache](https://mustache.github.io/)

> 如果可以的话，尽量避免使用JSP，当他们使用内嵌的Servlet容器时，有几个已知的[限制](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-jsp-limitations)

当您在默认配置下使用这些模板引擎之一时，您的模板会自动从src/main/resources/templates中获取。

> 根据你运行程序的方式，IntelliJ IDEA会对类路径进行不同的排序。在IDE中从main方法运行程序的顺序不同于使用Maven或Gradle或从jar运行的顺序。这可能导致SpringBoot无法从类路径中找到模板。如果你有这个问题，你要在IDE中重新整理下顺序，将模块中类和资源放在第一为。

---
2019.03.21
### 29.1.11 错误的处理
默认情况下，SpringBoot提供了一个/error映射它以合理的方式处理所有错误，并在servlet容器中注册为全局错误页面。对于机器客户端，它会生成一个JSON响应，其中包含详细的HTTP状态和异常信息。对于浏览器客户端，会产生一个以HTML形式展示的white标签错误页面(要自定义它，添加一个错误页面)。要完全替换默认行为，可以实现ErrorController并注册该类型的bean定义，或者添加ErrorAttibutes类型的bean使用现有的机制，但是要替换内容。

> BasicErrorController可以作为ErrorController的基类。如果你想为新的content-type添加一个处理程序（默认是text/html，并为其他所有内容提供后备）这一点尤其有用。为此，继承BasicErrorController，使用@RequestMapping添加一个具有produces属性的公共方法，并创建一个新类型的bean。

你还可以定义一个用@ControllerAdvice注释的类，以自定义JSON文档，为特定的控制器异常类型返回，如下所示：
```
@ControllerAdvice(basePackageClasses = AcmeController.class)
public class AcmeControllerAdvice extends ResponseEntityExceptionHandler {

	@ExceptionHandler(YourException.class)
	@ResponseBody
	ResponseEntity<?> handleControllerException(HttpServletRequest request, Throwable ex) {
		HttpStatus status = getStatus(request);
		return new ResponseEntity<>(new CustomErrorType(status.value(), ex.getMessage()), status);
	}

	private HttpStatus getStatus(HttpServletRequest request) {
		Integer statusCode = (Integer) request.getAttribute("javax.servlet.error.status_code");
		if (statusCode == null) {
			return HttpStatus.INTERNAL_SERVER_ERROR;
		}
		return HttpStatus.valueOf(statusCode);
	}

}
```
在前面的例子中，如果你的异常是由与AcmeController在同一个包中定义的控制器引发的，则使用客户的json表示形式pojo而不是ErrorAttributes表示形式。

#### 自定义错误页面
如果要为给定的状态代码显示自定义的HTML错误页，可以将文件添加到/error文件夹中。错误页面可以是静态的超文本标记语言(即添加在任何静态资源文件夹下)，也可以使用模板构建。文件的名称应该是确切的状态代码或系列掩码。

例如，要将404映射到静态超文本标记语言文件，您的文件夹结构如下:
```
src/
 +- main/
     +- java/
     |   + <source code>
     +- resources/
         +- public/
             +- error/
             |   +- 404.html
             +- <other public assets>
```
要使用自由标记模板映射所有5xx错误，您的文件夹结构如下:
```
src/
 +- main/
     +- java/
     |   + <source code>
     +- resources/
         +- templates/
             +- error/
             |   +- 5xx.ftl
             +- <other templates>
```
对于更复杂的映射，还可以添加实现ErrorViewResolver接口的beans，如下例所示:
```
public class MyErrorViewResolver implements ErrorViewResolver {

	@Override
	public ModelAndView resolveErrorView(HttpServletRequest request,
			HttpStatus status, Map<String, Object> model) {
		// Use the request or status to optionally return a ModelAndView
		return ...
	}

}
```
您还可以使用常规的Spring MVC特性，如@ExceptionHandler方法和@ControllerAdvice。然后，错误控制器拾取任何未处理的异常。

---


#### 在Spring MVC之外映射错误页面
对于不使用Spring MVC的应用程序，您可以使用ErrorPageRegistrar接口直接注册ErrorPages。这个抽象直接与底层的嵌入式servlet容器一起工作，即使您没有Spring MVC DispatcherServlet也能工作。
```
@Bean
public ErrorPageRegistrar errorPageRegistrar(){
	return new MyErrorPageRegistrar();
}

// ...

private static class MyErrorPageRegistrar implements ErrorPageRegistrar {

	@Override
	public void registerErrorPages(ErrorPageRegistry registry) {
		registry.addErrorPages(new ErrorPage(HttpStatus.BAD_REQUEST, "/400"));
	}

}
```
> 如果你使用过滤器来注册错误页面（比如一些非Spring框架中，比如Jersey和Wicket）这个过滤器必须注册为ERROR Dispatcher，如下所示：
```
@Bean
public FilterRegistrationBean myFilter() {
	FilterRegistrationBean registration = new FilterRegistrationBean();
	registration.setFilter(new MyFilter());
	...
	registration.setDispatcherTypes(EnumSet.allOf(DispatcherType.class));
	return registration;
}
```
注意：默认的FilterRegistrationBean 不包含ERROR dispatcher 类型

注意：当部署到servlet容器时，SpringBoot使用错误页面过滤器将错误的请求状态转发到错误页面。只有在尚未提交响应的情况下，才能将请求转发到正确的错误页面。默认情况下，WebSphere应用服务器8.0和更高版本在成功完成servlet的服务方法时提交响应。您应该通过设置来禁用此行为：
`com.ibm.ws.webcontainer.invokeFlushAfterService` 设置为 false.

### 29.1.12 Spring HATEOAS
如果你使用hypermedia开发了RESTful API，SpringBoot为Spring HATEOAS提供了自动装配能和大多数应用程序很好的工作。自动装配代替了@EnableHypermediaSupport的使用并注册了许多的bean来构建hypermedia应用程序，包括一个LinkDiscovers（用来支持客户端）和一个ObjectMapper，配置为将响应正确编成为所需的表示形式。ObjectMapper通过设置各种spring.jackson.*属性来定制，或者如果有一个存在通过Jackson2ObjectMapperBuilder  bean来定制。

你可以通过@EnableHypermediaSupport来实现对Spring HATEOAS的控制。注意，这样做会禁用前面描述的对象映射器自定义。

### 29.1.13 CORS支持
跨域资源共享（CORS） 大多数浏览器实现来W3C的规范，他允许你以灵活的方式指定哪种跨域请求被授权，而不是使用安全性较低，功能较弱的方法，如IFRAME或者JSOONP

在4.2版本中，Spring MVC支持CORS，只需在你的SpringBoot应用中的controller方法上添加@CrossOrign注解，而不需要任何其它的配置，全局的CORS配置可以通过注册WebMvcConfigurer bean自定义addCorsMappings(CorsRegistry)方法，如下所示：
```
@Configuration
public class MyConfiguration {

	@Bean
	public WebMvcConfigurer corsConfigurer() {
		return new WebMvcConfigurer() {
			@Override
			public void addCorsMappings(CorsRegistry registry) {
				registry.addMapping("/api/**");
			}
		};
	}
}
```
---
2019-03-30
### 29.2 “Spring WebFlux 框架”
在Spring框架5.0版本中介绍了WebFlux新型响应式Web框架，不同于Spring MVC，它不需要Servlet API，全程异步并且是非阻塞的，并通过Reactor项目实现了响应流的规范。

Spring WebFlux有两种风格：函数式和基于注解。基于注解的方式非常接近Spring MVC模型，如下所示：
```
@RestController
@RequestMapping("/users")
public class MyRestController {

	@GetMapping("/{user}")
	public Mono<User> getUser(@PathVariable Long user) {
		// ...
	}

	@GetMapping("/{user}/customers")
	public Flux<Customer> getUserCustomers(@PathVariable Long user) {
		// ...
	}

	@DeleteMapping("/{user}")
	public Mono<User> deleteUser(@PathVariable Long user) {
		// ...
	}

}
```
“WebFlux.fn”是函数式的一种变形，将路由配置与实际处理请求分开，如下所示:
```
@Configuration
public class RoutingConfiguration {

	@Bean
	public RouterFunction<ServerResponse> monoRouterFunction(UserHandler userHandler) {
		return route(GET("/{user}").and(accept(APPLICATION_JSON)), userHandler::getUser)
				.andRoute(GET("/{user}/customers").and(accept(APPLICATION_JSON)), userHandler::getUserCustomers)
				.andRoute(DELETE("/{user}").and(accept(APPLICATION_JSON)), userHandler::deleteUser);
	}

}

@Component
public class UserHandler {

	public Mono<ServerResponse> getUser(ServerRequest request) {
		// ...
	}

	public Mono<ServerResponse> getUserCustomers(ServerRequest request) {
		// ...
	}

	public Mono<ServerResponse> deleteUser(ServerRequest request) {
		// ...
	}
}

```

WebFlux是Spring框架的一部分，详细信息查看[参考文档](https://docs.spring.io/spring/docs/5.1.5.RELEASE/spring-framework-reference/web-reactive.html#webflux-fn)

> 你可以和大多数的RouterFunction beans一样模块化定义你的路由，如果你需要应用优先级，beans可以是有序的。

开始吧，在你的应用中添加spring-boot-starter-webflux模块

> 在你的应用中添加spring-boot-starter-web 和 spring-boot-starter-webflux模块，SpringBoot会自动装配Spring MVC而不是WebFlux。这种做是因为大多数Spring开发者将spring-boot-starter-webflux添加到他们Spring MVC应用程序中，使用响应式WebClien。你可以通过设置应用程序类型SpringApplication.setWebApplicationType(WebApplicationType.REACTIVE)来强化你的选择。

29.2.1 Spring WebFlux 自动装配

SpringBoot为Spring WebFlux提供了自动装配，适用于大多数应用。

自动装配在Spring默认设置的基础上增加了以下功能:
- 为HttpMessageReader 和 HttpMessageWriter配置解码器([本文稍后描述](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-webflux-httpcodecs))
- 支持静态服务资源，包括支持WebJars([本文稍后描述](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-spring-mvc-static-content))

如果你要保留SpringBoot WebFlux的特性并且你要添加新的[WebFlux配置](https://docs.spring.io/spring/docs/5.1.5.RELEASE/spring-framework-reference/web.html#web-reactive),你可以在你的WebFluxConfigurer类上添加@Configuration，但不包含@ EnableWebFlux。

如果你想完全控制Spring WebFlux，你可以在你的@Configuration上添加@EnableWebFlux

###  带有HttpMessageReaders和HttpMessageWriters的HTTP编解码器

SpringBoot使用HttpMessageReaders 和 HttpMessageWriters接口来对HTTP的request和response进行转化。它们通过CodecConfigurer进行配置，通过查看类路径中可用的库来获得合理的默认值。

SpringBoot通过CodecCustomizer实例来进一步定制。例如，配置spring.jackson.*的key应用到Jackson解码器

如果你需要添加自定义解码器，你可以创建一个自定义的CodecCustomizer组件，如下所示：
```
import org.springframework.boot.web.codec.CodecCustomizer;

@Configuration
public class MyConfiguration {

	@Bean
	public CodecCustomizer myCodecCustomizer() {
		return codecConfigurer -> {
			// ...
		}
	}

}
```
你还可以利用自定义JSON进行序列化和返序列号

#### 静态内容

默认情况下，Spring的静态服务内容在类路径下的/static或/public或/resources或/META-INF/resources中调用。它使用的是WevFlux中的ResourceWebHandler，因此你可以添加你的WebFluxConfigurer来覆盖addResourceHandlers方法。

默认情况下，资源映射到/**上，但是你可以通过spring.webflux.static-path-pattern属性来声明。例如，使用以下方式重新指定资源路径到/resources/**：
```
spring.webflux.static-path-pattern=/resources/**
```
你也可以使用spring.resources.static-locations自定义资源路径。这种做会用目录位置列表代替默认值。如果你这样做了，默认的欢迎页面将切换到你自定义的路径下。因此，在你启动时如果那个路径下有一个index.html，这个将是你应用程序的主页。

除了前面列出的“标准”静态资源位置，Webjars内容还有一个特例。任何在/webjars/** 路径中的资源，如果以webjar格式打包，则从jar文件中提供。

---
2019-03-31 

### 29.2.4 Template Engines