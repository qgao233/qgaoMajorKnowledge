# 事务

Spring支持：
* 编程式事务管理
* 声明式事务管理

两种方式。

声明式事务管理也有两种常用的方式：
* 一种是基于tx和aop名字空间的xml配置文件，
* 另一种就是基于@Transactional注解。

无论采用XML文件还是注解来配置事务，都需要完成下列步骤：

1. 配置数据源；
2. 配置事务管理器；
3. 配置事务增强；
4. 配置事务增强的切面。

**xml文件：**
```
<!-- 引入数据库配置文件jdbc.properties -->
<ctx:property-placeholder location="cLasspath:org/sL/netmarket/conf/jdbc.properties"></ctx:property-placeholder>
<!-- 配置数据源 -->
<bean id="datasource" class="org.apache.ibatis.datasource.pooled.PooledDataSource">
    <property name="driver" value="${driver}"></property>
    <property name=”url" value="${url}"></property>
    <property name="username" value="${user}"></property>
    <property name="password" value="${password}"></property>
</bean>
<!-- 配置事务管理器 -->
<bean id="transactionManager"
        class="org.springframework.jdbc.datasource.DataSourceTransactionManager”>
    <property name="dataSource" ref="datasource"></property>
</bean>
<!-- 配置事务增强 -->
<tx:advice id="txAdvice” transaction-manager="transactionManager">
    <tx:attributes>
        <tx:method name="sava*" propagations"REQUIRED”></tx:method>
        <tx:method name="query*” read-only="true”></tx:method>
        <tx:method name="remove*” propagations”REQUIRED”></tx:method>
        <tx:method name="modify” propagations”REQUIRED”></tx:method>
    </tx:attributes>
</tx:advice>
<!-- 配置事务增强的切面 Spring-AOP -->
<aop:config>
    <aop:pointeut id="txPointcut" expressions"execution(* org.sL.netmarket.services. *.*(..))"></aop:pointeut>
    <aop:advisor advice-ref="txAdvice" pointcut-ref="txPointcut"></aop:advisor>
</aop:config>
```
**注解式：**
1. 在要进行事务管理的方法前加上如@Transactional(propagation= Propagation.REQUIRED)
2. 在配置文件中指定驱动：<tx:annotation-driven transaction-manager="transactionManager" />

## 隔离级别

>注：
>* 脏读：事务B修改了某条数据行，还未提交，说不定之后还要修改，但在再次修改之前，事务A访问了这条数据行。
>* 不可重复读：事务A第一次读取了某条数据行并未提交，然后事务B修改了该数据行并进行提交，事务A再次进行读取该数据行，发现数据发生了变化。
>* 幻读：事务A第一次读取了5条数据，之后再执行同样的sql语句第二次读取，数据量少了或者多了。

* TransactionDefinition.ISOLATION_DEFAULT :使用后端数据库默认的隔离级别，
  1. MySQL 默认采用的 REPEATABLE_READ 隔离级别 
  2. Oracle 默认采用的 READ_COMMITTED 隔离级别
* TransactionDefinition.ISOLATION_READ_UNCOMMITTED :最低的隔离级别，使用这个隔离级别很少，因为它允许读取尚未提交的数据变更，
  >**读未提交**可能会导致脏读、不可重复读、幻读。
* TransactionDefinition.ISOLATION_READ_COMMITTED : 允许读取并发事务已经提交的数据，
  >**读提交**可以阻止脏读，
  >可能会导致不可重复读、幻读。
* TransactionDefinition.ISOLATION_REPEATABLE_READ : 对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，
  >**重复读**可以阻止脏读、不可重复读，
  >可能会导致幻读。
* TransactionDefinition.ISOLATION_SERIALIZABLE : 最高的隔离级别，完全服从 ACID 的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，
  >**串行**可以防止脏读、不可重复读以及幻读。但是这将严重影响程序的性能。通常情况下也不会用到该级别。

mysql的innodb实现**重复读**级别时，解决了幻读【[查看](../../chapter8/section10/index.md)】。

## 传播行为
为了解决业务层方法之间互相调用的事务问题。

当事务方法被另一个事务方法调用时，必须指定事务应该如何传播。例如：方法可能继续在现有事务中运行，也可能开启一个新事务，并在自己的事务中运行。

正确的事务传播行为可能的值如下:

1. TransactionDefinition.PROPAGATION_REQUIRED
   >使用的最多的一个事务传播行为，我们平时经常使用的@Transactional注解默认使用就是这个事务传播行为。如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。

2. TransactionDefinition.PROPAGATION_REQUIRES_NEW
   >创建一个新的事务，如果当前存在事务，则把当前事务挂起。也就是说不管外部方法是否开启事务，Propagation.REQUIRES_NEW修饰的内部方法会新开启自己的事务，且开启的事务相互独立，互不干扰。

3. TransactionDefinition.PROPAGATION_NESTED
   >如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于TransactionDefinition.PROPAGATION_REQUIRED。

4. TransactionDefinition.PROPAGATION_MANDATORY（很少使用）
   >如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常。（mandatory：强制性）

若是错误的配置以下 3 种事务传播行为，事务将不会发生回滚：

>* TransactionDefinition.PROPAGATION_SUPPORTS: 如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。
>* TransactionDefinition.PROPAGATION_NOT_SUPPORTED: 以非事务方式运行，如果当前存在事务，则把当前事务挂起。
>* TransactionDefinition.PROPAGATION_NEVER: 以非事务方式运行，如果当前存在事务，则抛出异常。