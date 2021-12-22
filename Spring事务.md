# 1.Spring中的事务

spring事务分为声明式事务和编程式事务，多用于声明式事务。

http://www.javaboy.org/2021/1026/spring_transaction.html

## 1.1 三大基础设施

Spring对事务的支持提供三个基础设施：

1. PlatformTransactionManager
2. TransactionDefinition
3. TransactionStatus

三个spring处理事务的核心类。

### 1.1.1 PlatformTransactionManager

![image-20211222151316098](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2021/image-20211222151316098.png)



```java
public interface PlatformTransactionManager {
   TransactionStatus getTransaction(@Nullable TransactionDefinition definition)
         throws TransactionException;

   void commit(TransactionStatus status) throws TransactionException;

   void rollback(TransactionStatus status) throws TransactionException;

}
```

定义了基本的事务操作方法，这些事务操作方法都是平台无关的，具体的实现都是由不同的子类来实现的。

+ getTransaction() 是根据传入的 TransactionDefinition 获取一个事务对象，TransactionDefinition 中定义了一些事务的基本规则，例如传播性、隔离级别等。
+ commit() 方法用来提交事务。
+ rollback() 方法用来回滚事务。



### 1.1.2 TransactionDefinition

`TransactionDefinition` 用来描述事务的具体规则，也称作事务的属性。

事务的属性：

+ 隔离性
+ 传播性
+ 回滚规则
+ 超时时间
+ 是否只读

TransactionDefinition中的方法：

![image-20211222152555344](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2021/image-20211222152555344.png)

+ getIsolationLevel()，获取事务的隔离级别
+ getName()，获取事务的名称
+ getPropagationBehavior()，获取事务的传播性
+ getTimeout()，获取事务的超时时间
+ isReadOnly()，获取事务是否是只读事务

![image-20211222152840532](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2021/image-20211222152840532.png)





### 1.1.3 TransactionStatus

TransactionStatus 可以直接理解为事务本身，该接口源码如下：

```java
public interface TransactionStatus extends SavepointManager, Flushable {
	boolean isNewTransaction();
	boolean hasSavepoint();
	void setRollbackOnly();
	boolean isRollbackOnly();
	void flush();
	boolean isCompleted();
}
```

+ isNewTransaction() 方法获取当前事务是否是一个新事务。

+ hasSavepoint() 方法判断是否存在 savePoint()。

+ setRollbackOnly() 方法设置事务必须回滚。

+ isRollbackOnly() 方法获取事务只能回滚。

+ flush() 方法将底层会话中的修改刷新到数据库，一般用于 Hibernate/JPA 的会话，对如 JDBC 类型的事务无任何影响。

+ isCompleted() 方法用来获取是一个事务是否结束。





# 2.事务属性

## 2.1 隔离级别

![image-20211222155842463](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2021/image-20211222155842463.png)

可以在`@Transactional(isolation = )`中指定此次事务的隔离级别



## 2.2 传播性

> 事务传播行为是为了解决业务层方法之间互相调用的事务问题，当一个事务方法被另一个事务方法调用时，事务该以何种状态存在？例如新方法可能继续在现有事务中运行，也可能开启一个新事务，并在自己的事务中运行，等等，这些规则就涉及到事务的传播性。

![image-20211222161117229](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2021/image-20211222161117229.png)

| 传播性         | 描述                                                         |
| :------------- | :----------------------------------------------------------- |
| REQUIRED(默认) | 如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务 |
| SUPPORTS       | 如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行 |
| MANDATORY      | 如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常 |
| REQUIRES_NEW   | 创建一个新的事务，如果当前存在事务，则把当前事务挂起，不管外部方法是否有事务，REQUIRES_NEW 都会开启自己的事务。 |
| NOT_SUPPORTED  | 以非事务方式运行，如果当前存在事务，则把当前事务挂起         |
| NEVER          | 以非事务方式运行，如果当前存在事务，则抛出异常               |
| NESTED         | 如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于 TransactionDefinition.PROPAGATION_REQUIRED |





## 2.3 回滚规则

默认情况下，事务只有遇到运行期异常（RuntimeException 的子类）以及 Error 时才会回滚，在遇到检查型（Checked Exception）异常时不会回滚。



## 2.4 是否只读

只读事务一般设置在查询方法上，但不是所有的查询方法都需要只读事务，要看具体情况。

一般来说，如果这个业务方法只有一个查询 SQL，那么就没必要添加事务，强行添加最终效果适得其反。

但是如果一个业务方法中有多个查询 SQL，情况就不一样了：多个查询 SQL，默认情况下，每个查询 SQL 都会开启一个独立的事务，这样，如果有并发操作修改了数据，那么多个查询 SQL 就会查到不一样的数据。此时，如果我们开启事务，并设置为只读事务，那么多个查询 SQL 将被置于同一个事务中，多条相同的 SQL 在该事务中执行将会获取到相同的查询结果。







# 3.注意事项

1. 事务只能应用到 public 方法上才会有效。
2. 事务需要从外部调用，**Spring 自调事务用会失效**。即相同类里边，A 方法没有事务，B 方法有事务，A 方法调用 B 方法，则 B 方法的事务会失效，这点尤其要注意，因为代理模式只拦截通过代理传入的外部方法调用，所以自调用事务是不生效的。
3. 建议事务注解 @Transactional 一般添加在实现类上，而不要定义在接口上，如果加在接口类或接口方法上时，只有配置基于接口的代理这个注解才会生效。