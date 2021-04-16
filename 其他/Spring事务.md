# Spring事务
id: spring-tx
title: Spring事务
date: 2015-08-10 22:37:15
tags: [Spring]
categories: 技术
---

## 事务传递
![](http://7xnocv.com1.z0.glb.clouddn.com/spring-tx-1.png)<!-- more -->
## 事务配置
分为2类：拦截器、注解
### 拦截器
``` xml
<!-- 配置事务管理器 -->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"></property>
</bean>

<!-- 配置事务属性 -->
<tx:advice id="txAdvice" transaction-manager="transactionManager">
    <tx:attributes>
        <!-- 定义查询方法只读 -->
        <tx:method name="query*" read-only="true"/>
        <tx:method name="find*" read-only="true"/>
        <tx:method name="get*" read-only="true"/>

        <!-- 主数据库操作 -->
        <tx:method name="save*" propagation="REQUIRED"/>
        <tx:method name="update*" propagation="REQUIRED"/>
        <tx:method name="delete*" propagation="REQUIRED"/>

        <!-- 其它方法使用默认事务策略 -->
        <tx:method name="*"/>

    </tx:attributes>
</tx:advice>

<!-- 配置切面 -->
<aop:config>
    <!-- 定义切面，所有的service的所有的方法 -->
    <aop:pointcut expression="execution(* com.test.spring.service.*.*(..))" id="txPointcut"/>
    <!-- 添加事务到切面上 -->
    <aop:advisor advice-ref="txAdvice" pointcut-ref="txPointcut"/>
</aop:config>
```

### 注解
``` xml
<!-- 配置事务管理器 -->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"></property>
</bean>
<!-- 使事务注解生效 -->
<tx:annotation-driven transaction-manager="transactionManager"/>
```

使用了Hibernate，class替换为org.springframework.orm.hibernate3.HibernateTransactionManager

``` java
@Transactional(propagation= Propagation.REQUIRED,rollbackFor=Exception.class,timeout=1,isolation= Isolation.DEFAULT)
	public void save(Map<String, Object> map) throws Exception {

	}
```

在要使用事务管理的类或者方法上增加代码@Transactional，Spring官方团队建议不要在接口使用。在类上使用@Transactional，类中的所有public方法都将使用事务。

在public方法上使用@Transactional，则该方法使用事务；非public方法使用@Transactional不会报错，但也不会使用事务，相当于“白做”。

## 事务传播
1. @Transactional(propagation=Propagation.REQUIRED)：默认的spring事务传播级别，如果上下文中已经存在事务，那么就加入到事务中执行，如果当前上下文中不存在事务，则新建事务执行，所以这个级别通常能满足处理大多数的业务场景。

2. @Transactional(propagation=PROPAGATION_SUPPORTS)：如果上下文存在事务，则支持当前事务，加入到事务执行，如果没有事务，则使用非事务的方式执行。所以说，并非所有的包在transactionTemplate.execute中的代码都会有事务支持。这个通常是用来处理那些并非原子性的非核心业务逻辑操作，应用场景较少。

3. @Transactional(propagation=PROPAGATION_MANDATORY)：该级别的事务要求上下文中必须要存在事务，否则就会抛出异常！配置该方式的传播级别是有效的控制上下文调用代码遗漏添加事务控制的保证手段。比如一段代码不能单独被调用执行，但是一旦被调用，就必须有事务包含的情况，就可以使用这个传播级别。

4. @Transactional(propagation=PROPAGATION_REQUIRES_NEW)：每次都要一个新的事务，每次都会新建一个事务，并且同时将上下文中的事务挂起，当新建事务执行完成以后，上下文事务再恢复执行。

5. @Transactional(propagation=PROPAGATION_NOT_SUPPORTED)：当前级别的特点是,如果上下文中存在事务，则挂起事务，执行当前逻辑，结束后恢复上下文的事务。这个级别有什么好处？可以帮助你将事务极可能的缩小。我们知道一个事务越大，它存在的风险也就越多，所以在处理事务的过程中，要保证尽可能的缩小范围。比如一段代码，是每次逻辑操作都必须调用的，比如循环1000次的某个非核心业务逻辑操作。这样的代码如果包在事务中，势必造成事务太大，导致出现一些难以考虑周全的异常情况。所以这个事务这个级别的传播级别就派上用场了，用当前级别的事务模板包起来就可以了。

6. @Transactional(propagation=PROPAGATION_NEVER)：一旦当前存在其他事务，就抛出runtime异常，强制停止执行！

7. @Transactional(propagation=PROPAGATION_NESTED)：如果上下文中存在事务，则嵌套事务执行，如果不存在事务，则新建事务。

## 数据库隔离级别

| 隔离级别                   | 脏读（Dirty Read） | 不可重复读（NonRepeatable Read） | 幻读（Phantom Read） |
| ---------------------- | -------------- | ------------------------- | ---------------- |
| 未提交读（Read uncommitted） | 可能             | 可能                        | 可能               |
| 已提交读（Read committed）   | 不可能            | 可能                        | 可能               |
| 可重复读（Repeatable read）  | 不可能            | 不可能                       | 可能               |
| 可串行化（Serializable ）    | 不可能            | 不可能                       | 不可能              |

| 名词    | 解释                                       |
| ----- | ---------------------------------------- |
| 脏读    | 一事务对数据进行了增删改，但未提交，另一事务可以读取到未提交的数据。如果第一个事务这时候回滚了，那么第二个事务就读到了脏数据 |
| 不可重复读 | 一个事务中发生了两次读操作，第一次读操作和第二次操作之间，另外一个事务对数据进行了修改，这时候两次读取的数据是不一致的 |
| 幻读    | 第一个事务对一定范围的数据进行批量修改，第二个事务在这个范围增加一条数据，这时候第一个事务就会丢失对新增数据的修改 |

## 数据库默认隔离级别
| 数据库       | 默认级别            |
| --------- | --------------- |
| MYSQL     | REPEATABLE_READ |
| SQLSERVER | READ_COMMITTED  |
| ORACLE    | READ_COMMITTED  |

## Spring事务隔离
@Transactional(isolation = Isolation.隔离级别)

| 隔离级别             | 说明                                       |
| ---------------- | ---------------------------------------- |
| DEFAULT          | 这是个 PlatfromTransactionManager 默认的隔离级别，使用数据库默认的事务隔离级别。 |
| SERIALIZABLE     | 事务串行执行，资源消耗最大                            |
| REPEATABLE_READ  | 保证了一个事务不会修改已经由另一个事务读取但未提交（回滚）的数据。避免了“脏读取”和“不可重复读取”的情况，但是带来了更多的性能损失。 |
| READ_COMMITTED   | 大多数主流数据库的默认事务等级，保证了一个事务不会读到另一个并行事务已修改但未提交的数据，避免了“脏读取”，该级别适用于大多数系统。 |
| READ_UNCOMMITTED | 这是事务最低的隔离级别，它充许另外一个事务可以看到这个事务未提交的数据。这种隔离级别会产生脏读，不可重复读和幻像读。 |
## Spring事务回滚
spring使用声明式事务处理，默认情况下，如果被注解的数据库操作方法中发生了unchecked异常，所有的数据库操作将rollback；如果发生的异常是checked异常，默认情况下数据库操作还是会提交的。

rollbackFor：该属性用于设置需要进行回滚的异常类数组，当方法中抛出指定异常数组中的异常时，则进行事务回滚。例如：
指定单一异常类：@Transactional(rollbackFor=RuntimeException.class)；
指定多个异常类：@Transactional(rollbackFor={RuntimeException.class, Exception.class})

rollbackForClassName：该属性用于设置需要进行回滚的异常类名称数组，当方法中抛出指定异常名称数组中的异常时，则进行事务回滚。例如：
指定单一异常类名称：@Transactional(rollbackForClassName=”RuntimeException”)；
指定多个异常类名称：@Transactional(rollbackForClassName={“RuntimeException”,”Exception”})

noRollbackFor：该属性用于设置不需要进行回滚的异常类数组，当方法中抛出指定异常数组中的异常时，不进行事务回滚。例如：
指定单一异常类：@Transactional(noRollbackFor=RuntimeException.class)；
指定多个异常类：@Transactional(noRollbackFor={RuntimeException.class, Exception.class})

noRollbackForClassName：该属性用于设置不需要进行回滚的异常类名称数组，当方法中抛出指定异常名称数组中的异常时，不进行事务回滚。例如：
指定单一异常类名称：@Transactional(noRollbackForClassName=”RuntimeException”)；
指定多个异常类名：@Transactional(noRollbackForClassName={“RuntimeException”,”Exception”})
