---
title: Mybatis 常见面试题
tags:
  - Mybatis
  - 面试题
categories:
  - [Mybatis]
  - [面试题]
abbrlink: 8f175f96
date: 2020-09-01 23:10:00
description: Mybatis常见面试题；支持自定义 SQL、存储过程以及高级映射
---
# 什么是 MyBatis？
MyBatis 是一款优秀的持久层框架，它支持自定义 SQL、存储过程以及高级映射。MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。
<!-- more -->
# Mybaits的优点
 1. 基于SQL语句编程，相当灵活，不会对应用程序或者数据库的现有设计造成任何影响，SQL写在XML里，解除sql与程序代码的耦合，便于统一管理；提供XML标签，支持编写动态SQL语句，并可重用。
 2. 与JDBC相比，减少了50%以上的代码量，消除了JDBC大量冗余的代码，不需要手动开关连接；
 3. 很好的与各种数据库兼容（因为MyBatis使用JDBC来连接数据库，所以只要JDBC支持的数据库MyBatis都支持）。
 4. 能够与Spring很好的集成；
 5. 提供映射标签，支持对象与数据库的ORM字段关系映射；提供对象关系映射标签，支持对象关系组件维护。    

# 常见面试题
## MyBatis与Hibernate有哪些不同
 1. Mybatis和hibernate不同，它不完全是一个ORM框架，因为MyBatis需要程序员自己编写Sql语句。
 2. Mybatis直接编写原生态sql，可以严格控制sql执行性能，灵活度高，非常适合对关系数据模型要求不高的软件开发，因为这类软件需求变化频繁，一但需求变化要求迅速输出成果。但是灵活的前提是mybatis无法做到数据库无关性，如果需要实现支持多种数据库的软件，则需要自定义多套sql映射文件，工作量大。 
 3. Hibernate对象/关系映射能力强，数据库无关性好，对于关系模型要求高的软件，如果用hibernate开发可以节省很多代码，提高效率。 
---
## #{}和${}的区别是什么?
 #{} 是预编译处理， \${} 是字符串替换。	
Mybatis在处理 #{} 时，会将sql中的 #{} 替换为 ? 号，调用PreparedStatement的set方法来赋值；	
Mybatis在处理 \${} 时，就是把 \${} 替换成变量的值。	
使用 #{} 可以有效的防止SQL注入，提高系统安全性。	
---
## 当实体类中的属性名和表中的字段名不一样 ，怎么办？
第1种： 通过在查询的sql语句中定义字段名的别名，让字段名的别名和实体类的属性名一致。
```xml
<select id=”"selectorder" parametertype="int" resultetype="top.jared.entity.order">
    select order_id id, order_no orderno ,order_price price form orders where order_id=#{id};
</select>
```
第2种： 通过`<resultMap>`来映射字段名和实体类属性名的一一对应的关系。
```xml
<select id="getOrder" parameterType="int" resultMap="orderresultmap">
    select * from orders where order_id=#{id}
</select>
<resultMap type="top.jared.entity.order" id="orderresultmap">
    <!–用id属性来映射主键字段–>
    <id property="id" column="order_id">
    <!–用result属性来映射非主键字段，property为实体类属性名，column为数据表中的属性–>
    <result property="orderno" column="order_no"/>
    <result property="price" column="order_price" />
</reslutMap>
```
---
## 模糊查询like语句该怎么写?
第1种：在Java代码中添加sql通配符。
```java
    String wildcardname = "%smi%";
    list<name> names = mapper.selectlike(wildcardname);
 
    <select id="selectlike">
     select * from foo where bar like #{value}
    </select>
```
第2种：在sql语句中拼接通配符，会引起sql注入
```java
    string wildcardname = "smi";
    list<name> names = mapper.selectlike(wildcardname);
 
    <select id="selectlike">
         select * from foo where bar like "%"${value}"%"
    </select>
```
---
## 通常一个Xml映射文件，都会写一个Dao接口与之对应，请问，这个Dao接口的工作原理是什么？Dao接口里的方法，参数不同时，方法能重载吗？
Dao接口即Mapper接口。接口的全限名，就是映射文件中的`namespace`的值；接口的方法名，就是映射文件中Mapper的Statement的id值；接口方法内的参数，就是传递给sql的参数。      

Mapper接口是没有实现类的，当调用接口方法时，接口全限名+方法名拼接字符串作为key值，可唯一定位一个MapperStatement。在Mybatis中，每一个`<select>`、`<insert>`、`<update>`、`<delete>`标签，都会被解析为一个MapperStatement对象。      
> 举例：com.mybatis3.mappers.StudentDao.findStudentById，可以唯一找到namespace为com.mybatis3.mappers.StudentDao下面 id 为 findStudentById 的 MapperStatement。
---
## Mybatis是如何进行分页的？分页插件的原理是什么？  
  Mybatis使用RowBounds对象进行分页，它是针对ResultSet结果集执行的内存分页，而非物理分页。可以在sql内直接书写带有物理分页的参数来完成物理分页功能，也可以使用分页插件来完成物理分页。
  分页插件的基本原理是使用Mybatis提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的sql，然后重写sql，根据dialect方言，添加对应的物理分页语句和物理分页参数。
---
## Mybatis是如何将sql执行结果封装为目标对象并返回的？都有哪些映射形式？
第一种是使用`<resultMap>`标签，逐一定义数据库列名和对象属性名之间的映射关系。 
第二种是使用sql列的别名功能，将列的别名书写为对象属性名。  
有了列名与属性名的映射关系后，Mybatis通过反射创建对象，同时使用反射给对象的属性逐一赋值并返回，那些找不到映射关系的属性，是无法完成赋值的。
---
## 如何执行批量插入?
首先,创建一个简单的insert语句:
```xml
<insert id="insertname">
  insert into names(name) values(#{value})
</insert>
```
然后在java代码中像下面这样执行批处理插入: 
```java
    list<string> names = new arraylist();
    names.add(“fred”);
    names.add(“barney”);
    names.add(“betty”);
    names.add(“wilma”);

    // 注意这里 executortype.batch
    sqlsession sqlsession = sqlsessionfactory.opensession(executortype.batch);
    try {
        namemapper mapper = sqlsession.getmapper(namemapper.class);
        for (string name : names) {
            mapper.insertname(name);
        }
        sqlsession.commit();
    }catch(Exception e){
        e.printStackTrace();
        sqlSession.rollback(); 
        throw e; 
    }finally {
        sqlsession.close();
    }
```
---
## 如何获取自动生成的(主)键值?
insert 方法总是返回一个int值 ，这个值代表的是插入的行数。  
如果采用自增长策略，自动生成的键值在 insert 方法执行完后可以被设置到传入的参数对象中。 
示例： 
```xml
<insert id="insertname" usegeneratedkeys="true" keyproperty="id">
    insert into names (name) values (#{name})
</insert>
```
```java
name name = new name();
name.setname("fred");

int rows = mapper.insertname(name);
// 完成后,id已经被设置到对象中
system.out.println("rows inserted = " + rows);
system.out.println("generated key value = " + name.getid());
```
---
