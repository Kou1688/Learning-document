[TOC]



## 1.Spring

### 	1.1 简介

+ Spring：春天——>给软件行业带来春天
+ 基于interface21框架
+ Spring理念：使现有的技术更加容易使用，本身是一个大杂烩，整合了现有的技术框架。
+ SSM：Spring+Spring MVC+Mybatis
+ 官网：spring.io

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.3.6</version>
</dependency>
```



### 	1.2 优点

+ Spring是一个开源的免费的框架（容器）！
+ Spring是一个轻量级的，非入侵式的框架！
+ 控制反转（IOC），面向切面编程（AOP）！
+ 支持事务的处理，对框架整合的支持！

==总结一句话：Spring就是一个轻量级的控制反转（IOC）和面向切面编程（AOP）的框架！==

### 	1.3 组成

![image-20210617155616795](C:\Users\Kou\AppData\Roaming\Typora\typora-user-images\image-20210617155616795.png)

 

### 	1.4 拓展

现代化的Java开发，说白就是基于Spring开发。

![image-20210617160137145](C:\Users\Kou\AppData\Roaming\Typora\typora-user-images\image-20210617160137145.png)

+ SpringBoot
  + 一个快速开发的脚手架
  + 基于SpringBoot可以快速开发单个微服务
  + 约定大于配置
+ SpirngCloud
  + 基于SpringBoot实现

现在大多数公司都在使用SpringBoot开发，学习SpringBoot的前提，需要完全掌握Spring及SpringMVC，承上启下。

弊端：发展太久，违背原来理念，配置十分繁琐。人称：<u>配置地狱</u>



## 2.IOC理论推导

代码：spring-01-ioc1

1. UserDao 接口

   ```java
   public interface UserDao {
       /**
        * 获取用户数据
        */
       void getUser();
   }
   
   ```

2. UserDaoImpl 实现类

   ```java 
   public class UserDaoImpl implements UserDao {
       @Override
       public void getUser() {
           System.out.println("默认获取用户数据");
       }
   }
   
   ```

3. UserService 业务接口

   ```java
   public interface UserService {
       /**
        * 获取用户数据
        */
       void getUser();
   }
   
   ```

4. UserServiceImpl 业务实现类

   ```java
   public class UserServiceImpl implements UserService {
       //若有若干个实现类，每次变动都要修改实例化的对象
       private UserDao userDao = new UserDaoImpl() ;
   
       @Override
       public void getUser() {
           userDao.getUser();
       }
   }
   ```

   

在之前业务中，用户需求可能会影响原来的代码，我们需要根据用户的需求去修改源代码。

如果程序代码量十分大，修改一次成本代价十分大。



使用一个set接口实现：已经发生了革命性变化

```java
private UserDao userDao ;

/**
  * 利用set进行动态实现值的注入
  * @param userDao dao层user
  */
public void setUserDao(UserDao userDao) {
    this.userDao = userDao;
}
```

+ 之前，程序是主动创建对象，控制权在程序员
+ 使用set注入后，程序不再具有主动性，而是变成了被动的接受对象

==控制反转==

这种思想从本质上解决了问题，程序员无需管理对象的创建。系统耦合性大大降低，专注于业务实现。这就是IOC的原型。

### 2.1 IOC本质

**控制反转IOC（Inversion of Control），是一种设计思想，DI（依赖注入）是实现IOC的一种方法**

所谓控制反转，就是获得依赖对象的方式反转了。



采用XML方式配置Bean的时候，Bean的定义信息是和实现分离的，而采用注解的方式可以把两者合为一体，Bean的定义信息直接以注解的形式定义在实现类中，从而达到零配置的目的。

**控制反转是一种通过描述（xml或注解）并通过第三方去生产或获取特定对象的方式，在Spring中实现控制反转的是IOC容器，其实现方法是依赖注入（Dependency Injection,DI）。**



------------------------------------------

## 3.HelloSpring

代码：spring-02-hellospring

+ hello对象由谁创建的？

  hello对象由Spring创建的。

+ Hello对象的属性是怎么设置的？

  hello对象的属性是由Spring容器设置的。

控制：谁来控制对象的创建，传统应用程序对象是由程序本身控制创建的，使用Spring后，对象是由Spring来创建的。

反转：程序本身不创建对象，变为被动的接收对象。

==由主动的编程变为被动的接收==

**所谓IOC：对象由Spring创建，管理，装配。**



--------------------------------------------------------

## 4.IOC创建对象的方式

代码：spring-03-ioc2

1. Spring默认使用无参构造来创建bean

2. 假设需要实现有参构造

   1. 下标赋值

      ```xml
      <bean id="user" class="com.kou.pojo.User">
          <constructor-arg index="0" value="Kou"/>
      </bean>
      ```

   2. 类型创建

      ```xml
      <bean id="user" class="com.kou.pojo.User">
          <constructor-arg type="java.lang.String" value="Kou"/>
      </bean>
      ```

   3. 通过参数名创建

      ```xml
      <bean id="user" class="com.kou.pojo.User">
      <constructor-arg name="name" value="Kou"/>
      </bean>
      ```



总结：在配置文件加载的时候，容器中管理的对象就已经初始化了。



## 5.Spring配置



### 5.1 别名

```xml
<!--别名-->
<alias name="user" alias="userNew"/>
```



### 5.2 Bean的配置

```xml
<!--
     id:bean的唯一标识符，相当于对象名
     class:bean对象所对应的全限定名:包名+类型
     name:别名 可以同时取多个别名，可以通过空格逗号分号分割
     -->
<bean id="userT" class="com.kou.pojo.UserT" name="user2,user3,user4">

</bean>
```



### 5.3 import

import，一般用于团队开发使用，可以将多个配置文件，导入合并为一个。

假设现在项目中有多个人开发，这三个人负责不同类开发，不同类注册在不同bean中，我们可以利用import语句将所有人的beans.xml合并为一个总的。

+ 张三
+ 李四
+ 王五
+ applicationContext.xml

使用的时候，直接使用总的配置



## 6.依赖注入



### 6.1 构造器注入

前面章节已经讲过



### 6.2 ==set方法注入【重点】==

代码：spring-04-di

+ 依赖注入：Set注入!

  + 依赖

    bean对象的创建依赖于容器

  + 注入

    bean对象中的所有属性，由容器来注入

【环境搭建】

1. 复杂类型

   ```java
   public class Address {
       private String address;
   
       public String getAddress() {
           return address;
       }
   
       public void setAddress(String address) {
           this.address = address;
       }
   }
   ```

   

2. 真实测试对象

   ```java
   public class Student {
       private String name;
       private Address address;
       private String[] book;
       private List<String> hobbies;
       private Map<String, String> card;
       private Set<String> game;
       private String wife;
       private Properties info;
   }
   ```

完整注入信息：

```xml
<bean id="address" class="com.kou.pojo.Address"/>

<bean id="student" class="com.kou.pojo.Student">
    <!--第一种，普通值注入.value-->
    <property name="name" value="寇超杰"/>
    <!--第二种，bean注入.ref-->
    <property name="address" ref="address"/>

    <!--数组-->
    <property name="book">
        <array>
            <value>红楼梦</value>
            <value>水浒传</value>
            <value>西游记</value>
        </array>
    </property>

    <!--List-->
    <property name="hobbies">
        <list>
            <value>吃饭</value>
            <value>睡觉</value>
        </list>
    </property>

    <!--Map-->
    <property name="card">
        <map>
            <entry key="Java" value="java"/>
            <entry key="身份证" value="2222"/>
        </map>
    </property>

    <!--Set-->
    <property name="game">
        <set>
            <value>LOL</value>
            <value>CSGO</value>
        </set>
    </property>

    <!--NULL值注入-->
    <property name="wife">
        <null/>
    </property>

    <!--Properties-->
    <property name="info">
        <props>
            <prop key="driver">202008110</prop>
            <prop key="url">寇超杰</prop>
            <prop key="username">寇超杰</prop>
            <prop key="password">寇超杰</prop>
        </props>
    </property>

</bean>
```



### 6.3 拓展方式注入

我们可以使用P命名空间和C命名空间进行注入

官方解释：

![image-20210703152945104](C:\Users\Kou\AppData\Roaming\Typora\typora-user-images\image-20210703152945104.png)

使用：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
       xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--p命名空间注入，可以直接注入属性的值:property-->
    <bean id="user" class="com.kou.pojo.User" p:name="Kou" p:age="18"/>

    <bean id="user2" class="com.kou.pojo.User" c:age="18" c:name="寇"/>
</beans>
```

测试:

```java
@Test
public void test2(){
    ApplicationContext context = new ClassPathXmlApplicationContext("userbeans.xml");
    User user=context.getBean("user2",User.class);
    System.out.println(user);
}
```

注意点：p命名和c命名空间不能直接使用，需要导入xml约束。

```xml
xmlns:p="http://www.springframework.org/schema/p"
xmlns:c="http://www.springframework.org/schema/c"
```



### 6.4 Bean的作用域

![image-20210703153703565](C:\Users\Kou\AppData\Roaming\Typora\typora-user-images\image-20210703153703565.png)

1. 单例模式（Spring默认机制）

   ```xml
   <bean id="accountService" class="com.something.DefaultAccountService" scope="singleton"/>
   ```

   

2. 原型模式：每次从容器get的时候，都会产生一个新对象。

   ```xml
   <bean id="accountService" class="com.something.DefaultAccountService" scope="prototype"/>
   ```

3. 其余的request，session，application，这些只能在web开发中使用到



-----------------------------------------

## 7.Bean的自动装配



+ 自动装配是Spring满足bean依赖的一种方式。
+ Spring会在上下文中自动寻找，并自动给bean装配属性。



在Spring中有三种自动装配的方式

1. 在xml中显式配置
2. 在Java中显示配置
3. 隐式的自动装配bean【重要！】



### 7.1 测试

环境搭建

+ 一个人有两个宠物

  

### 7.2 byName自动装配

```xml
<!--
    byName:会自动在容器上下文中查找，
    和自己对象set方法后面的值对应的bean id(set方法声明set后面跟的名字)
-->
<bean id="people" class="com.kou.pojo.People" autowire="byType">
    <property name="name" value="Kou"/>
</bean>
```

### 7.3 byType自动装配

```xml
<!--
	byType:会自动在容器上下文中查找，
    和自己对象属性类型相同的bean（可以省略id名）
-->
<bean id="people" class="com.kou.pojo.People" autowire="byType">
    <property name="name" value="Kou"/>
</bean>
```

小结：

+ byname时候，需要保证所有的bean的id唯一，并且这个bean需要和自动注入的set方法的值一致。
+ bytype时候，需要保证所有的bean的class唯一，并且这个bean需要和自动注入的属性的类型一致。



### 7.4 使用注解进行自动装配

jdk1.5支持的注解 spring2.5支持注解

The introduction of annotation-based configuration raised the question of whether this approach is “better” than XML.

要使用注解须知：

1. 导入约束 context约束

2. 配置注解的支持:==<context:annotation-config/>==

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context
           https://www.springframework.org/schema/context/spring-context.xsd">
   
       <context:annotation-config/>
   
   </beans>
   ```

   

@**Autowired**

直接在属性上使用即可。也可以在set上使用（==建议在set方法上使用==）

使用Autowired我们可以不用编写set方法（不建议），前提是你这个自动装配的属性在IOC容器中存在。默认为byType匹配

科普：

```java
@Nullable 字段标记了这个注解，说明这个字段可以为null
```

```java 
public @interface Autowired {
    boolean required() default true;
}
```

测试代码：

```java
public class People {
    @Autowired
    private Cat cat;
    @Autowired
    private Dog dog;
    private String name;
}
```

@Qualifier可以指定注入的对象 *byname*

**@Resource**

小结：

@Resource和@Autowired区别：

+ 都是用来自动装配的，都可以放在属性字段上
+ @Autowired优先通过byType实现，有多个实现类时，使用byName【常用】
+ Resource通过byName实现



## 8.使用注解开发

在Spring4之后，要使用注解开发，必须保证aop包导入

1. bean

   @**Component**

2. 属性如何注入

   ```java
   @Component
   public class User {
   
       @Value("Kou")
       public String name;
   }
   ```

   

3. 衍生的注解

   @Component有几个衍生注解：

   + dao：**@Repository**
   + service：**@Service**
   + controller：**@Controller**

4. 自动装配

5. 作用域

   **@Scope**

6. 小结

   xml与注解：

   + xml更加万能，适用于任何场合，维护简单方便
   
   + 注解，不是自己类使用不了，维护相对复杂
   
   + xml用来管理bean
   
   + 注解只负责完成属性的注入
   
     

## 9.使用Java的方式配置Spring

**@Configuration**：配置类注解，把这个Java类当作一个xml配置文件

获得spring上下文用AnnotationConfigApplicationContext中用 配置类.class

**@Bean**：组件注解，把当前bean交给ioc管理

getBean("方法名字")



------------------------

## 10.代理模式

代理模式：SpringAOP的底层。

代理模式分类：

+ 静态代理
+ 动态代理



### 10.1 静态代理

代码：spring-08-proxy

角色分析：

+ 抽象角色：一般会使用接口或抽象类解决
+ 真实角色：**被代理**的角色
+ 代理角色：代理真实角色，代理真实角色后，我们一般会做一些附属操作
+ 客户：访问被代理的人

可以使真实角色操作更加纯粹，不用关注一些公共业务。

公共业务交给代理角色，实现业务分工

缺点：一个真实角色会产生一个代理角色，代码量翻倍，开发效率变低

### 10.2 关于AOP

![image-20210705135204358](C:\Users\Kou\AppData\Roaming\Typora\typora-user-images\image-20210705135204358.png)



### 10.3 动态代理

代码spring-08-proxy.demo04

+ 动态代理和静态代理角色一样
+ 动态代理的代理类是动态生成的
+ 动态代理分为两大类：基于接口的动态代理，基于类的动态代理
  + 基于接口：JDK动态代理
  + 基于类：CGLIB动态代理

需要了解两个类：==Proxy：代理，InvocationHandler：调用处理程序==

**Proxy**：提供了创建动态代理类和实例的静态方法，它也是由这些方法创建的所有动态代理类的超类。

**InvocationHandler：**是由代理实例的调用处理程序实现的接口。每个代理实例都有一个关联的调用处理程序。当代理实例上调用方法时，方法调用将被编码并分派到其调用处理程序的invoke方法。

==动态代理就是利用反射实现==

优点：

+ 可以使真实角色操作更加纯粹，不用关注一些公共业务。

+ 公共业务交给代理角色，实现业务分工
+ 一个动态代理类代理的是一个接口，一般就是对应的一类业务

+ 一个动态代理类可以代理多个类，只要是实现了接口即可



-----------------------

## 11.AOP



### 11.1 什么是AOP

**面向切面编程**：通过预编译方式和运行期间动态代理实现程序功能的统一维护的一种技术

![image-20210705175508572](C:\Users\Kou\AppData\Roaming\Typora\typora-user-images\image-20210705175508572.png)



### 11.2 AOP在Spring中的作用

==提供声明式事务，允许用户自定义切面==

+ 横切关注点：与我们业务逻辑无关，但是我们需要关注的部分，如日志，安全，缓存，事务。
+ 切面：横切关注点被模块化的特殊对象，它是一个类（Log）
+ 通知：切面必须完成的工作。（log中的方法）
+ 目标：被通知的对象。（被代理的类）
+ 代理：向目标对象应用通知后创建的对象。（生成的代理类）
+ 切入点：切面通知指向的“地点”的定义。（在哪个地方执行）
+ 连接点：与切入点匹配的执行点。（在哪个地方执行）

![image-20210705180814700](C:\Users\Kou\AppData\Roaming\Typora\typora-user-images\image-20210705180814700.png)

即AOP在不改变原有代码的情况下，去增加新的功能。



### 11.3 使用Spring实现AOP

【**重点**】使用AOP织入，需要导入一个依赖包

**方式一：使用Spring的API接口**（主要使用Spring的接口）

```java
MethodBeforeAdvice   AfterReturningAdvice
```

代码spring-09-aop.log/service

```xml
<!--注册Bean-->
<bean id="userService" class="com.kou.service.UserServiceImpl"/>
<bean id="log" class="com.kou.log.Log"/>
<bean id="afterLog" class="com.kou.log.AfterLog"/>

<!--使用原生Spring API接口-->
<!--配置aop-->
<aop:config>
    <!--切入点 expression:表达式,execution(要执行的位置******)
        .*类中所有方法(..)方法中任意参数-->
    <aop:pointcut id="pointcut" expression="execution(* com.kou.service.UserServiceImpl.*(..))"/>

    <!--执行环绕-->
    <!--将log切入到pointcut中-->
    <aop:advisor advice-ref="log" pointcut-ref="pointcut"/>
    <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut"/>
</aop:config>
```



**方式二：使用自定义类实现(主要是切面)**

代码spring-09-aop.diy

```xml
<!--方法二：自定义类-->
<bean id="diy" class="com.kou.diy.DiyPointCut"/>

<aop:config>
    <!--定义切面 ref要引用的类-->
    <aop:aspect ref="diy">
        <aop:pointcut id="point" expression="execution(* com.kou.service.UserServiceImpl.*(..))"/>
        <!--通知-->
        <aop:before method="before" pointcut-ref="point"/>
        <aop:after method="after" pointcut-ref="point"/>
    </aop:aspect>
</aop:config>
```



**方式三：使用注解实现**

**@Aspect**

```java
@Aspect
public class AnnotationPointCut {
    @Before("execution(* com.kou.service.UserServiceImpl.*(..))")
    public void before(){
        System.out.println("方法执行前");
    }
}

```

环绕通知：

```java
/**
     * 环绕通知
     * @param joinPoint 连接点
     */
@Around("execution(* com.kou.service.UserServiceImpl.*(..))")
public Object around(ProceedingJoinPoint joinPoint) throws Throwable {
    System.out.println("环绕前");
    //获得签名
    Signature signature= joinPoint.getSignature();
    System.out.println(signature);
    //执行方法
    Object proceed = joinPoint.proceed();
    System.out.println("环绕后");
    return proceed;
}
```

AOP总之就是，在不影响原来业务类的情况下，实现业务的增强。



----------------------------

## 12.整合Mybatis

步骤：

1. 导入相关jar包

   + junit

   + mybatis

   + mysql数据库

   + spring相关

   + aop织入

   + mybatis-spring

     ```xml
     <dependencies>
         <dependency>
             <groupId>org.springframework</groupId>
             <artifactId>spring-webmvc</artifactId>
             <version>5.3.6</version>
         </dependency>
         <dependency>
             <groupId>junit</groupId>
             <artifactId>junit</artifactId>
             <version>4.12</version>
             <scope>test</scope>
         </dependency>
         <dependency>
             <groupId>org.aspectj</groupId>
             <artifactId>aspectjweaver</artifactId>
             <version>1.9.2</version>
         </dependency>
         <dependency>
             <groupId>mysql</groupId>
             <artifactId>mysql-connector-java</artifactId>
             <version>8.0.21</version>
         </dependency>
         <dependency>
             <groupId>org.mybatis</groupId>
             <artifactId>mybatis</artifactId>
             <version>3.5.2</version>
         </dependency>
         <dependency>
             <groupId>org.springframework</groupId>
             <artifactId>spring-jdbc</artifactId>
             <version>5.1.9.RELEASE</version>
         </dependency>
         <dependency>
             <groupId>org.mybatis</groupId>
             <artifactId>mybatis-spring</artifactId>
             <version>2.0.2</version>
         </dependency>
     </dependencies>
     ```

     

2. 编写配置文件

3. 测试



### 12.1 回忆Mybatis

1. 编写实体类
2. 编写核心配置文件
3. 编写接口
4. 编写Mapper.xml
5. 测试

