# 使用SpringBoot

> 更新时间：2019-02-21/22/23/24/25/26/27

这个小结我们将详细介绍如何使用SpringBoot。 包括了如何构建一个系统，自动配置，并且如何运行你的应用。我们也提供了SpringBoot最好练习。尽管SpringBoot没有什么特别之处（它只是你可以使用的一个库而已），这里有几条建议如果遵循会让你的开发过程更容易一些。

如果你从SpringBoot开始，你可能先要阅读[“快速开始”](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#getting-started)这一小结

### 13. 构建系统
强烈建议你选择一个可支持依赖管理并可以发布到“Maven Central”的工具。这里我们建议你使用Maven或者Gradle，其它工具（例如Ant）也可以帮助你构建系统，但是他们支持的不是特别好。

### 13.1 依赖管理
Spring Boot的每一个版本都提供了它所支持的依赖关系的精确列表。实际上，你不需要为你的构建中配置任何的依赖，就想SpringBoot帮你管理了一样。当你升级SpringBoot，这些依赖也会一起升级。

> 如果有需要的话，你也可以指定一个版本覆盖SpringBoot推荐的依赖

精选列表中包含了所有的SpringBoot模块，你可以使用SpringBoot一起使用，也可以使用第三方类库。该清单像标准的库一样可以和Maven和Gradle一起使用。

> 每次发布的SpringBoot版本都是和Spring框架相关联的，我们强烈建议你不要指定其版本。

### Maven
Maven用户可以继承`spring-boot-starter-parent`项目，以获得合理的默认值。这个父项目提供了以下癌特征：
- Java8是默认的编译级别
- UTF-8是代码编码格式
- 在[依赖管理小结](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#using-boot-dependency-management)，继承`spring-boot-dependencies`pom，管理共用的依赖版本。当你使用你自己的pom，依赖管理会让你省略那些依赖的<version>标签
- 使用repackage goal重新repackage执行id
- 合理的资源过滤
- 合理的插件配置（exec plugin，Git commit ID和shade）
- 对于特定的配置文件`application.properties`和`application.yml`合理的过滤(例如：`application-dev.properties`和`application-dev.yml`)

注意这些，因为`application-dev.properties`和`application-dev.yml`文件支持Spring`${...}`风格占位，Maven过滤被更改为`@...@`占位符（可以通过设置`resource.delimiter`来覆盖）

#### 13.2.1 继承父Starter
在你的项目配置中去继承`spring-boot-starter-parent`,设置`parent`如下所示：
```
<!-- 默认继承自SpringBoot -->
<parent>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-parent</artifactId>
	<version>2.1.3.RELEASE</version>
</parent>
```
> 你应该需要在这个依赖项上指定SpringBoot版本号，如果你是导入另外的starts，你可以放心的省略那些版本号

使用该设置，您还可以通过覆盖自己项目中的属性来覆盖各个依赖项。例如要升级到另一版Spring Data，你只要在`pom.xml`中添加以下内容：
```
<properties>
	<spring-data-releasetrain.version>Fowler-SR2</spring-data-releasetrain.version>
</properties>
```
> 在`spring-boot-dependencies`pom列表中检查支持的属性

#### 13.2.2 使用不带父POM的SpringBoot
不是所有人都喜欢继承`spring-boot-starter-parent` pom，你可能有你自己公司的标准父pom，或者你更愿意在Maven配置中声明所有的依赖。

如果你不想要使用`spring-boot-starter-parent`，通过使用`scope=import`依赖项，你可以继续保留对你有用的依赖管理（但不是插件管理），如下所示:
```
<dependencyManagement>
		<dependencies>
		<dependency>
			<!-- 从springboot中导入依赖管理 -->
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-dependencies</artifactId>
			<version>2.1.3.RELEASE</version>
			<type>pom</type>
			<scope>import</scope>
		</dependency>
	</dependencies>
</dependencyManagement>
```
如上所述，前面的示例设置不允许您使用属性覆盖单个依赖项。为了实现同样的结果，你需要在你项目的`spring-boot-dependencies`entry之前添加`dependencyManagement`entry。例如，升级到另一个版本的Spring Data，你可以在你的pomx中添加如下元素：

```
<dependencyManagement>
	<dependencies>
		<!-- Override Spring Data release train provided by Spring Boot -->
		<dependency>
			<groupId>org.springframework.data</groupId>
			<artifactId>spring-data-releasetrain</artifactId>
			<version>Fowler-SR2</version>
			<type>pom</type>
			<scope>import</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-dependencies</artifactId>
			<version>2.1.3.RELEASE</version>
			<type>pom</type>
			<scope>import</scope>
		</dependency>
	</dependencies>
</dependencyManagement>
```
> 在前面的例子中，我们指定了一个BOM，但是任何依赖类型都可以用同样的方式被覆盖。

#### 13.2.3 在springBoot中使用Maven插件

SpringBoot包含了Maven 插件它可以帮你打包成一个可执行的jar包。如果你需要使用可以在`<plugins>`添加它，如下所示：
```
<build>
	<plugins>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
		</plugin>
	</plugins>
</build>
```
> 如果你使用SpringBoot starter parent pom，你只需要添加这个插件。除非你想更改父级中定义的设置，否则无需对其进行配置。

### 13.3 Gradle
学习如何在SpringBoot中使用Gradle，请参考SpringBoot Gradel插件相关文档:

- 参考（[HTML](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/gradle-plugin/reference/html)和[PDF](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/gradle-plugin/reference/pdf/spring-boot-gradle-plugin-reference.pdf)
- [API](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/gradle-plugin/api)

### 13.4 Ant
可以使用Apache Ant+Ivy构建SpringBoot应用。`spring-boot-antlib`的“Antlib”模块也可以帮助你快速创建可执行jar包。

在`ivy.xml`中声明依赖，如下面这个例子：
```
<ivy-module version="2.0">
	<info organisation="org.springframework.boot" module="spring-boot-sample-ant" />
	<configurations>
		<conf name="compile" description="everything needed to compile this module" />
		<conf name="runtime" extends="compile" description="everything needed to run this module" />
	</configurations>
	<dependencies>
		<dependency org="org.springframework.boot" name="spring-boot-starter"
			rev="${spring-boot.version}" conf="compile" />
	</dependencies>
</ivy-module>
```

`build.xml`文件如下面这个例子：
```
<project
	xmlns:ivy="antlib:org.apache.ivy.ant"
	xmlns:spring-boot="antlib:org.springframework.boot.ant"
	name="myapp" default="build">

	<property name="spring-boot.version" value="2.1.3.RELEASE" />

	<target name="resolve" description="--> retrieve dependencies with ivy">
		<ivy:retrieve pattern="lib/[conf]/[artifact]-[type]-[revision].[ext]" />
	</target>

	<target name="classpaths" depends="resolve">
		<path id="compile.classpath">
			<fileset dir="lib/compile" includes="*.jar" />
		</path>
	</target>

	<target name="init" depends="classpaths">
		<mkdir dir="build/classes" />
	</target>

	<target name="compile" depends="init" description="compile">
		<javac srcdir="src/main/java" destdir="build/classes" classpathref="compile.classpath" />
	</target>

	<target name="build" depends="compile">
		<spring-boot:exejar destfile="build/myapp.jar" classes="build/classes">
			<spring-boot:lib>
				<fileset dir="lib/runtime" />
			</spring-boot:lib>
		</spring-boot:exejar>
	</target>
</project>
```
> 如果你不想使用`spring-boot-antlib`模块，可以参考[91.9小节.如何不用antlib构建](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#howto-build-an-executable-archive-with-ant)

### Starters
Starters是一组方便的依赖描述符，可以包含在你的应用中。您可以一站式使用所有Spring和相关技术，而无需搜索示例代码和复制粘贴依赖性描述符。例如，如果你像使用Spring JPA访问数据库，只需要在你的项目中包含`spring-boot-starter-data-jpa`即可。

starters包含了许多依赖项，需要这些依赖项才能让项目快速启动和运行，并具有一组一致的、受支持的托管传递依赖项

```
名字里面有什么？

在所有的官方starters中命名规则是：`spring-boot-starter-*`,其中*代表的是一个特殊类型的应用。当你需要查找某个starter时这种命名结构会帮助你找到预期中的starter，你可以在POM中按`ctrl+空格键`并输入“spring-boot-starter”以获得完整的列表。

```

在[创建你自己的starter](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-custom-starter)小节中介绍
，第三方的starters不会从SpringBoot中启动，因为它是Spring Boot官方保留的。然而，第三方的starter一般都是从项目名称开始，例如，`thridpartyproject`，那么starter命名为：`thridpartyproject-spring-boot-starter`

以下应用程序启动器由SpringBoot在`org.springframework.boot`组下提供:

##### 表 13.1.SpringBoot应用starters

| 名称                                   | 描述                                            | Pom     |
| -------------------------------------- | ----------------------------------------------- | ------- |
| spring-boot-starter                    | 核心starter，包含自动装配支持，日志，YAML       | [Pom]() |
| spring-boot-starter-activemq           | 使用Apache ActiveMQ消息                         | [Pom]() |
| spring-boot-starter-amqp               | 使用Spring AMQP                                 | [Pom]() |
| spring-boot-starter-aop                | Spring AOP面向切面编程                          | [Pom]() |
| spring-boot-starter-artemis            | 使用Spring artemis消息                          | [Pom]() |
| spring-boot-starter-batch              | 使用Spring Batch                                | [Pom]() |
| spring-boot-starter-catch              | 使用Spring 框架支持的缓存                       | [Pom]() |
| spring-boot-starter-connectors         | 使用Spring Cloud Connectors简化了云平台服务连接 | [Pom]() |
| spring-boot-starter-data-cassandra     | 分布式数据库和Spring 数据Cassandra              | [Pom]() |
| spring-boot-starter-data-elasticsearch | 使用ES引擎和Spring数据ES                        | [Pom]() |
| spring-boot-starter-data-jdbch         | 使用Spring Data JDBC                            | [Pom]() |
| spring-boot-starter-data-jdbch         | 在Hibernate中使用Spring Data JPA                | [Pom]() |
| ...                                    | ...                                             | ...     |

#### 表 13.2. SpringBoot产品级starters

| 名称                         | 描述                             | Pom  |
| ---------------------------- | -------------------------------- | ---- |
| spring-boot-starter-actuator | Actuator可以帮你管理监控你的应用 | 略   |

最后，如果你需要额外的或者特殊的，SpringBoot也提供了如下starter：
#### 表 13.3. SpringBoot 技术starters

| 名称                      | 描述                  | Pom     |
| ------------------------- | --------------------- | ------- |
| spring-boot-starter-jetty | 使用jetty servlet容器 | [Pom]() |
| spring-boot-starter-log4j | log4j日志组件         | [Pom]() |
| ...                       | ...                   | ...     |

> 更多社区贡献的starters清单，在GitHUB查看`spring-boot-starters`模块的readme文件

### 14.构造你的代码

SpringBoot不需要任何特定的代码布局就可以工作。然而，有一些最佳实践可能有所帮助。

### 14.1 使用默认的包
当一个类不包括包声明时，它被认为在默认包中，通常不鼓励用“默认包”，应该避免使用。对于使用@ComponentScan、@EntityScan或@SpringBootApplication注释的SpringBoot应用程序来说，这可能会导致特定的问题，因为每个jar中的每个类都被读取。

> 我们推荐包命名方式是把网站域名反过来（例如：com.example.project)

### 定位到主应用程序类
我们通常建议您将主应用程序类放在根包中，而不是其他类中。`@SpringBootApplication`一般注解都是放在主类上。例如你要写一个JPA应用。@SpringBootApplication注释类的包用于搜索@Entity项。使用根包还允许组件扫描仅应用于项目。

下面的列表展示了一个典型的布局：
```
com
 +- example
     +- myapplication
         +- Application.java
         |
         +- customer
         |   +- Customer.java
         |   +- CustomerController.java
         |   +- CustomerService.java
         |   +- CustomerRepository.java
         |
         +- order
             +- Order.java
             +- OrderController.java
             +- OrderService.java
             +- OrderRepository.java
```
这个“Application.java”文件声明在main方法，使用`@SpringBootApplication`，如下所示：
```
package com.example.myapplication;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}

}
```
### 配置class
SpringBoot支持Java配置。尽管SpringApplication可以和XML一起使用，我们通常建议你使用@Configuration类进行配置。通常定义主方法首选`@Configuration`

> 许多发布的案例中都是用XML来配置Spring的，如果可以的话，尝试使用`Ebable*`注解这可能是一个很好的开始，它具有和xml同样的功能

### 15.1 导入其它配置类
你不需要将所有的`@Configuration`放到一个类中，`@Import`注解可用于导入其他配置类。或者你可以使用`@ComponentScan`将所有的`@Configuration`类都包括进来

### 15.2 导入XML配置
如果你一定要使用XML配置，我们仍然建议你使用`@Configuration`类，你可以使用`@ImportResource`注解家在XML配置文件

### 16.自动装配
SpringBoot的自动装配会根据你添加的jar包依赖尝试去帮你配置Spring应用程序。例如，`HSQLDB`在你的classpath上，你不需要手动配置任何数据库连接，SpringBoot会自动在你的内存中配置一个数据库。

你需要通过将将`@EnableAutoConfiguration`和`@SpringBootApplication`注解添加到你的`@Configuration`类中自动配置

> 你应该值添加过`@EnableAutoConfiguration`或`@SpringBootApplication`注解。我们通常建议你只添加其中一个在你的`@Configuration`类中

### 16.1 逐步取代自动装配
自动装配是非入侵式的。在任何点，你都可以将你某部分的配置替换成自动装配。例如，如果你需要添加`Datasource` bean，默认的内嵌数据库就支持。

如果你需要知道当前应用什么地方需要自动装配或者为什么要自动装配？打开`--debug`模式启动你的应用。这样做可以启动debug日志，并将报告记录输出到控制台。

### 16.2 指定类禁用自动装配
如果你发现不需要自动配置的一个类正在被应用，你可以使用`@EnableAutoConfiguration`的`exclude`属性禁用他们，比如下面这个例子：
```
import org.springframework.boot.autoconfigure.*;
import org.springframework.boot.autoconfigure.jdbc.*;
import org.springframework.context.annotation.*;

@Configuration
@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
public class MyConfiguration {
}
```
> 你可以在注解级别使用`exclusions`这个属性

### 17. Spring Beans和依赖注入

你可以使任何Spring框架技术注入你的bean。简单的说，我们提供了`@ComponentScan`和`@Autowired`来帮你做的更好。

像上面的一样你可以在你的代码中添加`@ComponnetScan`不需要任何参数。你的应用组件（`@Component`,`@Service`,`@Repository`,`@Controller`等）都会被Spring进行管理。

下面这个例子显示了`@Service`构造器注入`RiskAssessor`bean
```
package com.example.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class DatabaseAccountService implements AccountService {

	private final RiskAssessor riskAssessor;

	@Autowired
	public DatabaseAccountService(RiskAssessor riskAssessor) {
		this.riskAssessor = riskAssessor;
	}

	// ...

}

```
如果一个bean只有一个构造器，你可以使用`@Autowired`,如下这个例子：
```
@Service
public class DatabaseAccountService implements AccountService {

	private final RiskAssessor riskAssessor;

	public DatabaseAccountService(RiskAssessor riskAssessor) {
		this.riskAssessor = riskAssessor;
	}

	// ...

}
```
> 注意，使用构造器进行注入，`riskAssessor`被final修饰，意味着将不能随意改动

### 18.使用@SpringBootApplication注解

大多数SpringBoot开发者喜欢在他们的app上使用自动装配，组件扫描，并且在他们的应用程序上进行额外的配置。一个`@SpringBootApplication`注解有以下三个特征：
- `@EnableAutoConfiguration`：启用[SpringBoot自动装配机制](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#using-boot-auto-configuration)
- `@ComponentScan`：启用组件扫描在应用程序包中[详情查看最佳实践](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#using-boot-structuring-your-code)
- `@Configuration`：允许在context注册额外的bean或者导入另外的配置类

因此使用`@SpringBootApplication`注解相当于使用了`@Configuration`, `@EnableAutoConfiguration`, 和` @ComponentScan`他们默认的属性，如以下案例：
```
package com.example.myapplication;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication // same as @Configuration @EnableAutoConfiguration @ComponentScan
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}

}
```

>  `@SpringBootApplication`也提供了别名来自定义`@EnableAutoConfiguration`和`@ComponentScan`的属性

> 这些特性不是必须的，你可以选择性的选择任何功能来代替某个注解。例如，在你的应用中可以不是用组件扫描

```
package com.example.myapplication;

import org.springframework.boot.SpringApplication;
import org.springframework.context.annotation.ComponentScan
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;

@Configuration
@EnableAutoConfiguration
@Import({ MyConfig.class, MyAnotherConfig.class })
public class Application {

	public static void main(String[] args) {
			SpringApplication.run(Application.class, args);
	}

}

这个例子中，Application就像其它任何SpringBoot程序一样，只是@Component注释不会自动检测到，用户定义的beans会显示导入（详情参考@Import）
```

### 19.运行你的应用

将你的应用程序打包成jar方式并使用内嵌的HTTP 服务是最好的高级功能之一。你可以像其它应用一样运行你的应用程序。调试SpringBoot应用也是简单的，你不需要任何特殊IDE或者扩展
> 这个小节只涵盖了基于jar的打包，如果你需要把你的应用打包成war文件，你应该参考你的服务器或IDE文档

### 19.1 从IDE中运行

你可以从你的IDE中运行SpringBoot就像一个简单的Java程序一样。然而，你第一步需要导入你的项目。接下来在你的IDE导入依赖来构建系统。大多数IDE可以直接导入Maven项目。例如，使用Eclipse 选择`File`菜单 `Import`->`Existing Maven Projects`

> 如果你不小心运行了两个应用程序，你会看见“Port already in use”错误。STS用户可以使用“重新启动”按钮而不是“运行”按钮来确保关闭任何现有实例。

### 19.2运行打包后的应用
如果你的SpringBoot使用了Maven和Gradle创建一个可执行的jar包，你可以使用`java -jar`命令运行程序，如下所示：
```
$ java -jar target/myapplication-0.0.1-SNAPSHOT.jar
```
这也支持远程调试下运行你打包后的应用。这样做可以让您将调试器附加到打包的应用程序，如下所示；
```
$ java -Xdebug -Xrunjdwp:server=y,transport=dt_socket,address=8000,suspend=n \
       -jar target/myapplication-0.0.1-SNAPSHOT.jar
```
### 19.3 使用Maven插件
SpringBoot Maven插件可以帮你快速编译并运行你的应用。就像它们在你的IDE中一样。下面的示例显示了运行Spring Boot应用程序的典型Maven命令:
```
mvn spring-boot:run
```
您可能还想使用MAVEN_OPTS操作系统环境变量,如下所示：
```
 export MAVEN_OPTS=-Xmx1024m
```

### 19.4使用Gradle 插件
Spring Boot Gradle插件包括`bootRun`任务，该任务可以用于以分解形式运行应用程序。每当应用`org.springframework.boot`和`java`插件时，都会添加`bootRun`任务，如下例所示:
```
gradle bootRun
```
您可能还想使用`JAVA_OPTS`操作系统环境变量,如下所示：
```
export JAVA_OPTS=-Xmx1024m
```
### 19.5 热部署
由于Spring Boot应用程序只是普通的Java应用程序，JVM热部署应该开箱即用。JVM热部署在某种程度上受限于它可以替换的字节码。对于更完整的解决方案，可以使用[JReleant](https://zeroturnaround.com/software/jrebel/)。

`spring-boot-devtools`模块也支持快速重启。查看本章后面[开发者工具](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#using-boot-devtools)小节，了解更多细节

### 20.开发者工具
Spring Boot包括一组额外的工具，可以让应用程序开发体验更加愉快。`spring-boot-devtools`模块可以包含在任何项目中，以提供额外的开发时特性。要支持开发工具，在你的构建中添加模块依赖，如下Maven和Gradle所示：

Maven. 
```
<dependencies>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-devtools</artifactId>
		<optional>true</optional>
	</dependency>
</dependencies>
```
Gradle.
```
<dependencies>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-devtools</artifactId>
		<optional>true</optional>
	</dependency>
</dependencies>
```
> 当你运行打包后的应用程序，开发工具会自动禁用。如果应用程序是从`java -jar`或者从特殊的类加载器启动，那么他会认为这是一个“生产应用”。在Maven中将依赖项标记为可选的或者在Gradle配置中自定义为“仅开发”（如上所示）这是防止开发工具过度使用项目的最佳做法。

> 默认情况下，重新打包的档案不会包含开发工具。如果你想使用远程开发工具的特性，你需要禁止`excludeDevtools`构建属性来包含它，Maven和插件都支持该属性。

### 20.1默认属性

SpringBoot提供了一些库使用缓存来提高性能。例如，模版引擎缓存编译后的模板来避免重复解析模板文件。另外，Spring MVC会缓存HTTP头来响应静态资源的访问。

在生产中缓存是非常有用的，但是在开发过程中反而适得其反，它会让你看到的应用看不到你做出的改变。基于这个原因，Spring Boot devtool默认是禁止使用缓存的

通常缓存选项在你的`application.properties`文件中，例如，Thymeleaf提供`spring.thymeleaf.cache`属性而不需要你手动去设置，`spring-boot-devtools`模块会自动的应用开发配置。

因为在开发Spring MVC和Spring WebFlux应用程序时，您需要更多关于web请求的信息，开发者工具会为`web`日志组启动`debug`日志，将显示每个请求的相关信息，被哪个处理以及响应结果等等。如果你记录所有请求的细节（包括潜在的敏感信息）你可以打开`spring.http.log-request-details`配置属性。

> 如果你不想使用属性的默认值，你可以在你的`application.properties`配置文件中设置`spring.devtools.add-properties`为false

> 有关devtools完整的属性列表，请查看[DevToolsPropertyDefaultsPostProcessor](https://github.com/spring-projects/spring-boot/tree/v2.1.3.RELEASE/spring-boot-project/spring-boot-devtools/src/main/java/org/springframework/boot/devtools/env/DevToolsPropertyDefaultsPostProcessor.java)

### 自动重启
无论classpath上的哪个文件发生了改变，应用会使用`spring-boot-devtools`自动重启。这在IDE中是个非常有用的功能，可以快速响应你代码的变化。默认情况下，任何在classpath中的文件都会被监控到。注意：某些资源（比如静态资源或这模版页面）不需要重启应用程序。

```
触发重启

当DevTools监视classpath资源时，触发重启的唯一方法是更新类路径。因为classpath而引起的更新依赖于你的IDE。在Eclipse中，保存修改的文件会触发重启。在IntelliJ IDEA中编译这个项目(`Build`->`Build Project`)也会有同样的效果。
```

> 只要启用了分叉，您也可以通过使用支持的构建插件( Maven和Gradle )来启动应用程序。因为DevTools需要一个独立的应用程序类加载器才能正常运行。默认情况下，Gradle和Maven在类路径上检测到DevTools时会这么做。

> 当你和LiveReload一起使用时自动重启将非常棒。查看[LiveReload小结](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#using-boot-devtools-livereload)了解更多细节。如果你是用JRebel，自动重启被重启，有利于动态类加载。其它开发工具特征（例如LiveReload属性覆盖）仍然可以使用。

> DevTools依赖于应用程序上下文的关闭挂钩来在重启过程中关闭它。如果禁用了关闭挂钩，则无法正常工作(`SpringApplication.setRegisterShutdownHook(false`)).

> 当决定classpath上的条目是否应该在它改变时触发重启时,DevTools会自动忽略项目名称如：`spirng-boot`,`spring-boot-devtools`,`spring-boot-autoconfigure`,`spring-boot-actuator`和`spring-boot-stater`

> DevTools需要使用`ApplicationContext`自定义`ResourceLoad`。如果你的应用已经提供了一个，它将被包裹起来，不会直接覆盖`ApplicationContext``getResource`方法``

```
重启和重载

SpringBoot提供的重启技术通过使用两个类加载器工作。不变的类(例如，来自第三方jar包的类)被加载到基类加载器中。你开发中正在使用的类被加载到重启类加载器中。重新启动应用程序时，重新启动类加载器将被丢弃，并创建一个新的类加载器。这种方法意味着应用程序重启通常比“冷启动”快得多，因为基类加载器已经可用并被填充。

如果您发现重新启动对于您的应用程序来说不够快或者遇到类加载问题，你可以考虑重加载技术，比如ZeroTurnaround的JRender。这些工作是通过在加载类时重写类来实现的，以使它们更易于重新加载。
```

### 20.2.1 记录条件评估中的变化

默认情况下，每次应用程序重新启动时，都会记录一份显示条件评估增量的报告。这个报告会显示你应用中发生改变的比如新增或移除bean或者设置了配置文件属性

下面这个属性，可以禁止生成这个报表：
```
spring.devtools.restart.log-condition-evaluation-delta=false
```

### 20.2.2 排除资源
某些资源不一定需要在更改时触发重新启动。例如，Thymeleaf可以编辑，默认情况下，改变`/META-INF/maven, /META-INF/resources, /resources, /static, /public, /templates`中的资源不会触发重启，但是livereload会被触发。如果你需要自定义排除资源，你可以使用`spring.devtools.restart.exclude`属性，例如，排除`/static`和`/public`你可以这样设置：
```
spring.devtools.restart.exclude=static/**,public/**
```
> 如果你想保持那些默认的资并添加俳除项，使用`spring.devtools.restart.additional-exclude`属性代替

### 20.2.3 禁止重启
如果你不想使用重启功能，你可以使用禁止`spring.devtools.restart.enabled`属性。在大多数情况中，你可以在你的`application.properties`设置这个属性（这样仍然会初始化重启类加载器，但是他不会观察文件的变化）

如果需要完全禁用重启支持（例如，因为它不适用于特定的库）你需要在调用`SpringApplication.run(…)`前把System中的`spring.devtools.restart.enabled`属性设置为false，如下所示：
```
public static void main(String[] args) {
	System.setProperty("spring.devtools.restart.enabled", "false");
	SpringApplication.run(MyApp.class, args);
}
```

### 20.2.5 触发文件
如果你想在IDE继续更改文件，你可能更想在特定的时候启动它，这样做，你可以使用“触发文件”，这是一个特殊的文件，当你真正触发重启检查时，一定要修改它。改变文件只会触发它检查，但是要重启必须让DevTools做些什么。触发文件可以手动更新或者使用IDE插件。

使用这个触发文件，在你触发文件路径上设置`spring.devtools.restart.trigger-file`属性

> 你可能需要对`spring.devtools.restart.trigger-file`进行[全局设置](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#using-boot-devtools-globalsettings)，这样你所有的项目都以同样的方式运行。

### 20.2.6 自定义重启类加载器
如前面 Restart vs Reload小节，重启功能是通过两个类加载器来是实现的。对于大多数应用，这种方法很有效。然而，有些时候加载器会有问题。

默认情况下，IDE中打开任何项目都会加载“重启”类加载器，所有的`.jar`文件都会被加载到`基础`类加载器中，如果你在一个多模块项目上工作，并不是所有模块都会被导入到你的IDE中，你可能需要自定义一些东西，为此，你可以创建一个`META-INF/spring-devtools.properties`文件

`spring-devtools.properties`文件可以包含前缀为`restart.exclude`和`restart.include`。`include`元素会被拉进“重启”类加载器,而`exclude`元素会别下推到“基本”类加载器中。属性的值以正则的方式被应用到classpath中，如下所示：
```
restart.exclude.companycommonlibs=/mycorp-common-[\\w-]+\.jar
restart.include.projectcommon=/mycorp-myproj-[\\w-]+\.jar
```

> 属性的key必须是唯一的，只要属性是以`restart.exclude`或`restart.include`开头

> 所有的`META-INF/spring-devtools.properties`从classpath中被加载。你可以在你的项目中打包，或者是项目中的库。

### 20.2.7 已知限制

重新启动功能不适用于使用标准`ObjectInputStream`反序列化的对象。如果你需要序列化数据，你需要结合Spring`ConfigurableObjectInputStream`和`Thread.currentThread().getContextClassLoader()`一起使用

不幸的是，几个第三方库在不考虑上下文类加载器的情况下进行反序列化。如果你发现这样的问题，你需要向原始作者请求修复。

### LiveReload
`spring-boot-devtools`模块包括一个嵌入式LiveReload服务器，当资源改变时，该服务器可以用来触发浏览器刷新。

LiveReload的Chrome、Firefox和Safari都可以从[官网](http://livereload.com/extensions/)免费获得LiveReload浏览器扩展。

当你的应用正在运行时你不需要启动LiveReload时，可以将`spring.devtools.livereload.enabled`属性设置为false

> 同一时间你只可以启动一个LiveReload。在启动你的应用之前，确认其它的LiveReload没在运行。如果你的IDE开启了多个应用，那么只LiveReload仅支持其中一个

### 20.4 全局设置

你可以通过添加文件名`.spring-boot-devtools.properties`在你的`$HOME`文件夹下（注意：文件名要以“.”开始）的方式来配置devtools全局功能。添加到这个文件的任何属性都可以在你的机器上应用到SpringBoot中。例如，配置始终从触发文件中重启，你可以添加如下属性：

##### ~/.spring-boot-devtools.properties. 
```
spring.devtools.reload.trigger-file=.reloadtrigger
```
> 在`.spring-boot-devtools.properties`激活的配置不会影响到已经加载的[特殊配置文件](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#boot-features-external-config-profile-specific-properties)

### 20.5 远程应用
SpringBoot开发者工具不仅仅先限制在本地开发，当你的应用在远程运行时你也可以使用不同的功能。远程支持时可选的。去启动它，你需要把`devtools`包含在配置中，如下所示：
```
<build>
	<plugins>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
			<configuration>
				<excludeDevtools>false</excludeDevtools>
			</configuration>
		</plugin>
	</plugins>
</build>
```
你还需要设置`spring.devtools.remote.secret`属性，如下所示：
```
spring.devtools.remote.secret=mysecret
```
> 远程启动应用是有安全风险的。你应该不会在你的生产中支持该功能。

远程开发工具支持分为两个部分：接受连接的服务器端端点和在ide中运行的客户端应用程序。当`spring.devtools.remote.secret`属性设置后服务组件会启动的，客户端组件需要手动启动。

### 20.5.1 运行远程客户端应用
远程客户端应用程序旨在从IDE中运行。您需要运行`org.springframework.boot.devtools.RemoteSpringApplication`与您连接的远程项目相同的类路径运行,应用程序唯一需要的参数是它所连接的远程URL。


例如，如果你正在使用Eclipse或STS，并且你有一个项目名称为`my-app` 部署到Cloud Foundry中，你需要执行一下操作：
- 从`Run`菜单选择`Run Configuration...`
- 创建新的Java应用程序“启动配置”
- 在浏览器中访问你的`my-app`项目
- 使用` org.springframework.boot.devtools.RemoteSpringApplication`作为主类
- 添加`https://myapp.cfapps.io`到代码参数中（或者任何远程URL）

正在运行的远程客户端可能类似于以下列表:
```
 .   ____          _                                              __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _          ___               _      \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` |        | _ \___ _ __  ___| |_ ___ \ \ \ \
 \\/  ___)| |_)| | | | | || (_| []::::::[]   / -_) '  \/ _ \  _/ -_) ) ) ) )
  '  |____| .__|_| |_|_| |_\__, |        |_|_\___|_|_|_\___/\__\___|/ / / /
 =========|_|==============|___/===================================/_/_/_/
 :: Spring Boot Remote :: 2.1.3.RELEASE

2015-06-10 18:25:06.632  INFO 14938 --- [           main] o.s.b.devtools.RemoteSpringApplication   : Starting RemoteSpringApplication on pwmbp with PID 14938 (/Users/pwebb/projects/spring-boot/code/spring-boot-devtools/target/classes started by pwebb in /Users/pwebb/projects/spring-boot/code/spring-boot-samples/spring-boot-sample-devtools)
2015-06-10 18:25:06.671  INFO 14938 --- [           main] s.c.a.AnnotationConfigApplicationContext : Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@2a17b7b6: startup date [Wed Jun 10 18:25:06 PDT 2015]; root of context hierarchy
2015-06-10 18:25:07.043  WARN 14938 --- [           main] o.s.b.d.r.c.RemoteClientConfiguration    : The connection to http://localhost:8080 is insecure. You should use a URL starting with 'https://'.
2015-06-10 18:25:07.074  INFO 14938 --- [           main] o.s.b.d.a.OptionalLiveReloadServer       : LiveReload server is running on port 35729
2015-06-10 18:25:07.130  INFO 14938 --- [           main] o.s.b.devtools.RemoteSpringApplication   : Started RemoteSpringApplication in 0.74 seconds (JVM running for 1.105)
```

> 因为这个客户端像真实应用一样所以可以直接读取应用属性。`spring.devtools.remote.secret`属性会被读取并传递到服务器进行验证

> 我们始终建议使用`https://`作为协议，一次传输过程中会被加密密码不能被拦截


> 如果你需要使用代理来访问远程应用，配置`spring.devtools.remote.proxy.host `和`spring.devtools.remote.proxy.port`属性

### 20.5.2 远程更新
远程客户端监控你应用上classpath发生改变会像本地一样重启。任何资源被推送到远程应用都会重启（如果需要的话）。如果您迭代使用本地没有的云服务的功能，这可能会很有帮助。通常，远程更新和重新启动比完整的重建和部署周期快得多。

> 只有远程客户端在运行的时候才会监控文件。如果你在远程客户端启动之前改变了文件，则不会推送到远程服务器。

### 21.打包你的生产应用
可执行的jar包可以在生产环境中部署，他们都是自带容器的，也适用于云部署

其它的“生产就绪”功能，比如监控，审核，REST或JMX端点，考虑到`spring-boot-actuator`查看第五部分，[SpringBoot Actuator](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#production-ready)更多细节。

### 接下来读什么
现在你应该理解如何使用SpringBoot并且遵循一些最佳实践。你可以去更深入的了解SpringBoot特性，或者你可以跳过前面阅读[SpringBoot](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/reference/htmlsingle/#production-ready)准备生产方面