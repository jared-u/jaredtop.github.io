---
title: 通用Mapper 逆向工程
tags:
  - Mybatis
  - mapper
  - mybatis插件
categories:
  - - mapper
  - - mybatis插件
  - - Mybatis
comments: true
description: 使用该插件可以很方便的生成实体类、Mapper接口以及对应的XML文件。
abbrlink: f2bf6669
date: 2020-10-25 13:38:21
top:
---

# MYBATIS GENERATOR 插件

使用该插件可以很方便的生成实体类、Mapper接口以及对应的XML文件。

## 使用通用 Mapper 插件

**引入依赖：**

```
<dependency>
            <groupId>tk.mybatis</groupId>
            <artifactId>mapper-spring-boot-starter</artifactId>
            <version>2.1.5</version>
</dependency>
```

## 使用Maven执行MBG

**配置pom.xml:**

```xml
<properties>
        <java.version>1.8</java.version>
        <!-- ${basedir} 引用工程跟目录 -->
        <!-- targetJavaProject ：声明存放源码的地方-->
        <targetJavaProject>${basedir}/src/main/java</targetJavaProject>
        <!-- targetMapperPackage: 声明MBG生成XxxMapper接口后存在的package位置 -->
        <targetMapperPackage>com.jared.mapper.mapper</targetMapperPackage>
        <!-- targetModelPackage： 声明MBG生成实体类后存放的package位置 -->
        <targetModelPackage>com.jared.mapper.entity</targetModelPackage>
        <!-- targetResourcesProject声明存放资源文件和XML配置文件的目录位置 -->
        <targetResourcesProject>${basedir}/src/main/resources</targetResourcesProject>
        <!-- targetXMLPackage:声明存在具体XxxMapper.xml文件的目录位置 -->
        <targetXMLPackage>mapper</targetXMLPackage>
        <!--  依赖版本  -->
        <mapper.version>4.1.5</mapper.version>
        <mysql.version>5.1.47</mysql.version>
    <!--这里配置了MBG配置文件中常用到的几个路径以及包名。还包含了通用Mapper的版本和数据库JDBC驱动的版本。-->
</properties>
<dependencies>
        <!--web依赖-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.1.3</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>

        <dependency>
            <groupId>tk.mybatis</groupId>
            <artifactId>mapper-spring-boot-starter</artifactId>
            <version>2.1.5</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
</dependencies>

<build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <!--配置了MBG插件，并且配置了generatorConfig.xml配置文件的路径。另外还有两个依赖，分别是JDBC驱动以及通用Mapper-->
            <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.3.7</version>
                <configuration>
                    <configurationFile>${basedir}/src/main/resources/generator/generatorConfig.xml</configurationFile>
                    <overwrite>true</overwrite>
                    <verbose>true</verbose>
                </configuration>
                <dependencies>
                    <dependency>
                        <groupId>mysql</groupId>
                        <artifactId>mysql-connector-java</artifactId>
                        <version>${mysql.version}</version>
                    </dependency>
                    <dependency>
                        <groupId>tk.mybatis</groupId>
                        <artifactId>mapper</artifactId>
                        <version>${mapper.version}</version>
                    </dependency>
                </dependencies>
            </plugin>
        </plugins>
</build>
```

## generatorConfig.xml配置文件

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    <!-- 引入外部属性文件 -->
    <properties resource="config.properties"/>

    <context id="Mysql" targetRuntime="MyBatis3Simple" defaultModelType="flat">
        <property name="beginningDelimiter" value="`"/>
        <property name="endingDelimiter" value="`"/>

        <!-- 配置通用Mapper的MBG插件相关信息 -->
        <plugin type="${mapper.plugin}">
            <property name="mappers" value="${mapper.Mapper}"/>
        </plugin>
        <!-- 数据库相关信息 -->
        <jdbcConnection driverClass="${jdbc.driverClass}"
                        connectionURL="${jdbc.url}"
                        userId="${jdbc.user}"
                        password="${jdbc.password}">
        </jdbcConnection>

        <!-- 配置Java实体类存放位置-->
        <javaModelGenerator
                targetPackage="${targetModelPackage}"
                targetProject="${targetJavaProject}"/>
        <!-- 配置XxxMapper.xml存放位置-->
        <sqlMapGenerator
                targetPackage="${targetXMLPackage}"
                targetProject="${targetResourcesProject}"/>
        <!-- 配置XxxMapper.java存放位置-->
        <javaClientGenerator
                targetPackage="${targetMapperPackage}"
                targetProject="${targetJavaProject}"
                type="XMLMAPPER" />
        <!-- 配置根据数据库表生成Java文件的相关规则-->
        <!-- tableName="%" 表示数据库中所有表都参与逆向工程，此时使用默认规则 -->
        <!-- 默认规则：table_dept->TableDept-->
        <!-- 不符合默认规则时需要使用tableName和domainObjectName两个属性明确指定 -->
        <table tableName="table_emp" domainObjectName="Employee" >
            <!-- 配置主键生成策略-->
            <generatedKey column="emp_id" sqlStatement="Mysql" identity="true"/>
        </table>
    </context>
</generatorConfiguration>
```

使用`<properties resource="config.properties"/>`引入了`config.properties`属性配置，该文件内容如下：

## config.properties

```properties
# 数据库配置
jdbc.driverClass = com.mysql.jdbc.Driver
jdbc.url = jdbc:mysql://localhost:3306/common_mapper?useUnicode=true&useSSL=false
jdbc.user = root
jdbc.password = 123456

#c3p0
jdbc.maxPoolSize=50
jdbc.minPoolSize=10
jdbc.maxStatements=100
jdbc.testConnection=true

# 通用Mapper配置
mapper.plugin = tk.mybatis.mapper.generator.MapperPlugin
mapper.Mapper = com.jared.mapper.common.mapper.BaseMapper
```

在pom.xml这一级目录的命令行窗口执行`mvn mybatis-generator:generate`即可（前提是配置了mvn）。

或者IDEA双击插件

![IDEA双击插件](http://img.jared.top/202010251432_429.png)

成功生成结果

![成功生成结果](http://img.jared.top/202010251433_179.png)