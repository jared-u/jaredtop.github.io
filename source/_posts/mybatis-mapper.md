---
title: 通用Mapper
tags:
  - Mybatis
  - mapper
  - mybatis插件
categories:
  - - mapper
  - - mybatis插件
  - [Mybatis]
comments: true
description: >-
  通用mapper
  可以极大的方便开发人员进行ORM，提供极其方便的单表增删改查。什么是通用mapper，一句话简单说，它就是个辅助mybatis极简单表开发的组件。它不是为了替代mybatis，而是让mybatis的开发更方便。
abbrlink: 886e92c3
date: 2020-10-23 15:15:06
top:
---
# 通用Mapper
## 环境搭建
### 构建springboot+mybatis项目
### 创建Java实体类
```java
@Table(name="table_emp") //mybatis通用接口mapper依赖JPA实体类采用JPA
public class Emploee {
    @Id
    private Integer empId;
    @Column(name= "emp_name")
    private String empName;
    @Column(name= "emp_salary")
    private Double empSalary;
    @Column(name= "emp_age")
    private Integer empAge;

    public Emploee() {
    }

    public Emploee(Integer empId, String empName, Double empSalary, Integer empAge) {
        this.empId = empId;
        this.empName = empName;
        this.empSalary = empSalary;
        this.empAge = empAge;
    }
    // 省略getters和setters toString
}
```
### 引入依赖
```xml
<dependency>
            <groupId>tk.mybatis</groupId>
            <artifactId>mapper-spring-boot-starter</artifactId>
            <version>2.1.5</version>
</dependency>
```
### 创建公用接口继承通用Mapper和MySqlMapper
```java
public interface BaseMapper<T> extends Mapper<T>, MySqlMapper<T> {
}
```
### application.properties配置
```xml
# mysql配置
spring.datasource.url=jdbc:mysql://localhost:3306/common_mapper?useUnicode=true
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
# mybatis配置
# 实体类位置
mybatis.type-aliases-package=com.jared.mapper.entity
# 公用接口类路径
mapper.mappers=com.jared.mapper.common.mapper.BaseMapper
mapper.identity=MYSQL
```
### 创建普通mapper
```java
/**
 * @description: 员工通用接口继承公用接口,注意泛型
 * @author Jared
 * @create 2020-10-23 16:21
 */
@Repository
public interface EmployeeMapper extends BaseMapper<Emploee> {
}
```
### 创建服务层接口
```java
public interface EmployeeService {
    Emploee getOne(Emploee employeeQueryCondition);
}

```
### 创建服务实现层接口
```java
@Service
public class EmployeeServiceImpl implements EmployeeService {
    @Autowired
    private EmployeeMapper employeeMapper;

    @Override
    public Emploee getOne(Emploee employeeQueryCondition) {
        return this.employeeMapper.selectOne(employeeQueryCondition);
    }
}
```

## 测试接口
### 根据实体类对象封装数据查询结构
```java
@Test
public void testSelectOne(){
    //1.创建封装查询条件的实体类对象
    Emploee employeeQueryCondition = new Emploee(null,"bob",5560.11,null);
    //2.执行查询
    Emploee emploeyeQueryResult=employeeService.getOne(employeeQueryCondition);
    //3.打印
    System.out.println(emploeyeQueryResult);
}
```
