---
title: 通用mapper常用注解和方法
tags:
  - Mybatis
  - mapper
  - mybatis插件
categories:
  - - mapper
  - - mybatis插件
  - [Mybatis]
comments: true
description: 通用mapper 常用的注解，例如@Table注解
abbrlink: 83d0a7ff
date: 2020-10-24 17:22:11
top:
---

# 常用注解

## @Table注解

**作用：**建立实体类和数据库表之间的对应关系。

**默认规则：**实体类类名首字母小写作为表明。Employee类→employee表

**用法：**在@Table注解的name属性中指定目标数据库表的表明

![@Table注解](http://img.jared.top/202010241915_794.png)



## @Column注解

**作用：**建立实体类字段和数据库表字段之间的对于关系

**默认规则：**

- 实体类字段：驼峰式命名
- 数据库表字段：使用“-”区分各个单词

**用法：**在@Column注解的name属性中指定目标字段的字段名

![@Column注解](http://img.jared.top/202010241913_362.png)



## @Id注解

通用Mapper在执行xxxByPrimaryKey(key)方法时，有两种情况。

情况1：没有使用@Id注解明确指定主键字段

```sql
SELECT emp_id,emp_name,emp_salary,emp_age FROM table_emp WHERE emp_id = ? AND emp_name = ? AND emp_salary = ? AND emp_age = ?
```

之所以会生成上面这样的WHERE子句是因为通用Mapper将实体类中的所有字段都拿来放在一起作为联合主键。

情况2：使用@Id注解明确标记和数据库表中主键字段对应的实体类字段。

![@Id注解](http://img.jared.top/202010241903_753.png)



## @GeneratedValue注解

**作用：**让通用Mapper在执行insert操作之后将数据库自动生成的主键值回写到实体类对象中。

**自增主键方法：**

![](http://img.jared.top/202010242012_726.png)

**序列主键用法：**

```java
@Id
@GeneratedValue(
    strategy = GenerationType.IDENTITY,
	generator = "select SEQ_ID.nextval from dual")
private Integer empId;
```

应用场景：购物车结账

- 增加商品销量...
- 减少商品库存...
- 生成订单数据v→封装到Order对象中→保存Order对象→数据库自动生成主键值→回写到实体类对象Order中
- 生成一系列订单详情数据→List<OrderItem>→在每一个OrderItem中设置Order对象的主键值作为外键→批量保存List<OrderItem>
- ...



## @Transient注解（JPA）

忽略非数据库字段



# 常用方法

## selectOne方法

**通用Mapper替我们自动生成的sql语句情况**

```sql
==>  Preparing: SELECT emp_id,emp_name,emp_salary,emp_age FROM table_emp WHERE emp_name = ? AND emp_salary = ?
==> Parameters: bob(String), 5560.11(Double)
<==    Columns: emp_id, emp_name, emp_salary, emp_age
<==        Row: 3, bob, 5560.11, 40
<==      Total: 1
```

**实体类封装查询条件生成WHERE子句的规则**

-  使用非空的值生成WHERE子句
-  在条件表达式中使用“=”进行比较

**要求必须返回一个实体类结果，如果有多个，则会抛出异常**



## xxxByPrimaryKey 方法

需要使用@Id注解明确标记和数据库主键字段对应的实体类字段，否则通用Mapper会将所有实体类字段作为联合主键。

## xxxSelective 方法

非主键字段如果为null值，则不加入到SQL语句中。





