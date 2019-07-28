[TOC]

### SprinBoot集成Mybatis Generator 逆向生成

> 当我们在数据库创建一张表，往往还要生成一个实体类、mapper，xml等文件，工作量多且重复不谈，有时难免会出错，有了这一款插件[Mybatis Generator](http://www.mybatis.org/generator/index.html)，可以解决这个问题,官网提供了多种配置方式选择，我这里使用的maven的方式

### 准备
- IDEA
- JDK8
- Maven3.5.4
- SpringBoot

### pom.xml配置
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.eleven.springbootmbg</groupId>
	<artifactId>springbootmbg</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>

	<name>springbootmbg</name>
	<description>Demo project for Spring Boot mybatis generator</description>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.1.0.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
			<version>1.3.2</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-jdbc</artifactId>
			<version>4.3.12.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>org.mybatis.generator</groupId>
			<artifactId>mybatis-generator-core</artifactId>
			<version>1.3.5</version>
		</dependency>
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

    <build>
        <finalName>Mybatis Generator</finalName>
        <pluginManagement>
            <plugins>
                <plugin>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-maven-plugin</artifactId>
                </plugin>
            </plugins>
        </pluginManagement>
        <plugins>
            <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.3.2</version>
                <configuration>
                    <!--配置文件路径-->
                    <configurationFile>src/main/resources/generatorConfig.xml</configurationFile>
                    <verbose>true</verbose>
                    <overwrite>true</overwrite>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```
### datasource.xml配置
> 这里主要是指定下generatorConfig.xml中用到的配置信息
```
#MySQL的JDBC驱动包，用JDBC连接MySQL数据库时必须使用该jar包
db.location=/Users/liuziqing/.m2/repository/mysql/mysql-connector-java/5.1.25/mysql-connector-java-5.1.25.jar
db.className=com.mysql.jdbc.Driver
db.url=jdbc:mysql://localhost:3306/demo?characterEncoding=utf-8
db.username=root
db.password=root
```

### generatorConfig.xml配置
> 直接从网上找了一份[原文](https://www.cnblogs.com/SacredOdysseyHD/p/8460644.html)
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    <!--导入属性配置-->
    <properties resource="datasource.properties"></properties>

    <!--指定特定数据库的jdbc驱动jar包的位置-->
    <classPathEntry location="${db.location}"/>

    <context id="default" targetRuntime="MyBatis3">

        <!-- optional，旨在创建class时，对注释进行控制 -->
        <commentGenerator>
            <property name="suppressDate" value="true"/>
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>

        <!--jdbc的数据库连接 -->
        <jdbcConnection
                driverClass="${db.className}"
                connectionURL="${db.url}"
                userId="${db.username}"
                password="${db.password}">
        </jdbcConnection>


        <!-- 非必需，类型处理器，在数据库类型和java类型之间的转换控制-->
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>

        <!-- 生成模型的包名和位置-->
        <!-- Model模型生成器,用来生成含有主键key的类，记录类 以及查询Example类
            targetPackage     指定生成的model生成所在的包名
            targetProject     指定在该项目下所在的路径
        -->
        <javaModelGenerator targetPackage="com.eleven.springbootmbg.entity" targetProject="./src/main/java">
            <!-- 是否允许子包，即targetPackage.schemaName.tableName -->
            <property name="enableSubPackages" value="false"/>
            <!-- 是否对model添加 构造函数 -->
            <property name="constructorBased" value="true"/>
            <!-- 是否对类CHAR类型的列的数据进行trim操作 （去空）-->
            <property name="trimStrings" value="true"/>
            <!-- 建立的Model对象是否 不可改变  即生成的Model对象不会有 setter方法，只有构造方法 -->
            <property name="immutable" value="false"/>
        </javaModelGenerator>

        <!--mapper映射文件生成所在的目录 为每一个数据库的表生成对应的SqlMap文件 -->
        <sqlMapGenerator targetPackage="mapper" targetProject="./src/main/resources">
            <property name="enableSubPackages" value="false"/>
        </sqlMapGenerator>

        <!-- 客户端代码，生成易于使用的针对Model对象和XML配置文件 的代码
                type="ANNOTATEDMAPPER",生成Java Model 和基于注解的Mapper对象
                type="MIXEDMAPPER",生成基于注解的Java Model 和相应的Mapper对象
                type="XMLMAPPER",生成SQLMap XML文件和独立的Mapper接口
        -->

        <!-- targetPackage：mapper接口dao生成的位置 -->
        <javaClientGenerator type="XMLMAPPER" targetPackage="com.eleven.springbootmbg.mapper" targetProject="./src/main/java">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false" />
        </javaClientGenerator>

        <!-- 要生成的表 tableName是数据库中的表名 domainObjectName是实体类名 默认会生成很多，不需要的可以false-->
        <table tableName="user" domainObjectName="user" enableCountByExample="false" enableUpdateByExample="false" enableDeleteByExample="false" enableSelectByExample="false" selectByExampleQueryId="false"></table>
        <!--<table tableName="mmall_product" domainObjectName="Product" enableCountByExample="false" enableUpdateByExample="false" enableDeleteByExample="false" enableSelectByExample="false" selectByExampleQueryId="false">-->
            <!--&lt;!&ndash; 数据库中该字段的类型是 txt ,不同版本生成对应字体类的属性类型可能不同，因此指定转换类型 &ndash;&gt;-->
            <!--<columnOverride column="detail" jdbcType="VARCHAR" />-->
            <!--<columnOverride column="sub_images" jdbcType="VARCHAR" />-->
        <!--</table>-->
    </context>
</generatorConfiguration>
```
### 生成文件
**它是覆盖式生成的，注意在pom.xml中配置**，两种方式:
- 使用maven命令
```
mvn mybatis-generator:generate
```
- 双击这个插件

![](https://raw.githubusercontent.com/liueleven/study/master/%E5%9B%BE%E5%BA%93/16-Mybatis/01-mybatisgenerator.png)

### 源码地址
[源码地址](https://github.com/liueleven/SpringBoot-MyBatisGenerator)