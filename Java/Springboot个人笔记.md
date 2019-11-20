前八章：

入门、配置、日志、web、docker、数据访问、启动配置原理、自定义starters

后八章：

缓存、消息、检索、任务（邮件、定时）、安全、分布式、开发热部署、监控管理

# 一、Spring Boot 入门

## 1.Spring Boot简介

> 简化Spring应用开发的一个框架；
>
> 整个Spring技术栈的一个大整合；
>
> J2EE开发的一站式解决方案；

## 2.微服务

2014，martin fowler

微服务：架构风格（服务微化）

一个应用应该是一组小型服务；可以通过HTTP的方式进行互通；

单体应用：ALL IN ONE

微服务：每一个功能元素最终都是一个可独立替换和独立升级的软件单元；

[详细参照微服务文档](https://martinfowler.com/articles/microservices.html#MicroservicesAndSoa)



使用Spirng boot 构建环境，然后使用Spring Cloud进行互联互调，DataFlow进行数据批处理

## 3.环境准备

#### 1.maven设置

给maven的settings.xml配置文件的Profiles标签添加

```xml
<profile>
  <id>jdk-1.8</id>
  <activation>
    <activeByDefault>true</activeByDefault>
    <jdk>1.8</jdk>
  </activation>
  <properties>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
  </properties>
</profile>
```

### 2.idea设置

在idea中配置maven，否则idea会使用自带的maven

## 4.Spring BootHelloWorld

一个功能：浏览器发送请求，服务器接收请求并处理，相应HelloWorld字符串

### 1.创建一个maven工程（jar）

Eable Auto import ：写入pom文件，会自动导入相应的包

### 2.导入相应的包

```xml
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.1.RELEASE</version>
    </parent>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
```

### 3.编写主程序

```java
package com.atguigu;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class HelloWorldApplication {
    public static void main(String[] args) {
        //启动spirng应用
        SpringApplication.run(HelloWorldApplication.class,args);
    }
}

```

### 4.编写相关的Controller、Service

```java
package com.atguigu;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class HelloWorldApplication {
    public static void main(String[] args) {
        SpringApplication.run(HelloWorldApplication.class,args);
    }
}
```

### 5.运行主程序

### 6.简化部署

```xml
 <!-- 这个插件，可以将应用打包成一个可执行的jar包；-->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```

将这个jar包打包之后直接使用Java -jar的命令进行运行

## 5.HelloWorld探究

### 1.pom文件

#### 1.父项目

```xml
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.1.RELEASE</version>
</parent>

它的父项目是dependencies,
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.2.1.RELEASE</version>
    <relativePath>../../spring-boot-dependencies</relativePath>
  </parent>
其中包含一个properties标签：
<properties>
    <activemq.version>5.15.10</activemq.version>
    <bitronix.version>2.1.4</bitronix.version>
    ........
    ........
    <byte-buddy.version>1.10.2</byte-buddy.version>
    <caffeine.version>2.8.0</caffeine.version>
    <cassandra-driver.version>3.7.2</cassandra-driver.version>
    <classmate.version>1.5.1</classmate.version>
  </properties>
```

Spring Boot 版本的仲裁中心，以后我们导入以来默认是不需要写版本的（没有在里面什么版本的，需要自己进行申明）

#### 2.启动器

```xml
<dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
```

spring-boot-starter:   spirng boot 场景启动器，帮我们导入了web模块相关的依赖



Springboot将所有的功能场景都抽取出来，做成一个个的starters（启动器）,只需要在项目里引入这些starter相关场景的所有依赖都会导入进来。要用什么功能就导入什么场景的启动器。

### 2.主程序类,主入口类

```java
package com.atguigu;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class HelloWorldApplication {
    public static void main(String[] args) {
        SpringApplication.run(HelloWorldApplication.class,args);
    }
}
```

@SpringBootApplication: SpringBoot应用 标注在某个类上说明这个类是SpringBoot的主配置类，SpringBoot就应该运行这个类的main方法来启动SpringBoot应用：

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {
```

@**SpringBootConfiguration：SpringBoot**的配置类；

​			标注在某个类上，表示是SpringBoot的配置类；

​			@**Configuration**:配置类上来标注这个注解

​			配置类  =======  配置文件；配置类也是容器中的一个组件；@Component

@**EnableAutoConfiguration** 开启自动配置功能

以前需要配置的东西，SpringBoot帮我们自动配置；不然自动配置不会生效；

```java
@AutoConfigurationPackage
@Import({AutoConfigurationImportSelector.class})
public @interface EnableAutoConfiguration {
```

@**AutoConfigurationPackage** 

​           @**Import**({Registrar.class}):Spirng的底层注解@import,给容器导入一个组件，导入的组件由AutoConfigurationPackages.Registrar.class决定

​                   将主配置类（@SpringBootAppliation标注的类）的所在包及下方所有的子包中的所有组件扫描到Spring容器

@**Import**({AutoConfigurationImportSelector.class})

​               给容器中导入组件，AutoConfigurationImportSelector：导入哪些组价的选择器

​               将所有需要导入的组件以全类名的方式返回；这些组件就会被添加到容器中；

​               会给容器导入非常多的自动配置类（XXXAutoConfiguration）：就是给容器中导入这个场景需要的所有组件，并配置好这些组件

​				有了自动配置类，免去了我们手动编写配置注入功能组件等的工作；

​				SpringFactoriesLoader.loadFactoryName（EnableAutoConfiguration.class，classLoader）：SpirngBoot在启动的时候从类路径下的META-INF/spirng.factories中获取EnableAutoConfiguration指定的值，将这些值作为自动配置类导入到容器中，自动配置就生效，帮我们进行自动配置工作；以前我们需要自己配置的东西，自动配置类都帮我们做了

J2EE的整体整合和解决方案都在spring-boot-autoconfiguration-2.2.1.RELEASE.jar

