[TOC]



# SSM



## 1.Spring



### 1.1 框架

**半成品软件**

高度抽取可重用代码的一种设计；高度的通用性；

多个可重用模块的集合，形成一个某个领域的整体解决方案；



### 1.2 Spring框架

是一个容器（可以管理所有的组件(类)）框架：

核心关注：==IOC和AOP==

优点：

+ 非侵入式
+ 依赖注入
+ 面向切面编程
+ 容器
+ 组件化
+ 一站式



### 1.3 IOC和DI概述

#### IOC：（Inversion of control）控制反转

控制：获取资源的方式；

+ 主动式

  要什么资源都自己创建

  复杂对象的创建是比较庞大的工程



+ 被动式

  资源的获取不是我们自己创建，而是交给容器创建和设置



容器：管理所有的组件（有功能的类）；容器可以自动的探查出哪些组件（类）需要用到另一些组件（类）；容器帮助我们创建对象，并且把对象赋值过去；

主动的new资源变为被动的接收资源；



#### DI：（Dependency Injection）依赖注入

容器能知道哪个组件（类）在运行时，需要另一个组件（类）；容器通过反射的形式，将容器中准备好的对象注入（利用反射给属性赋值）到类中。



只要是容器管理的组件，都能使用容器提供的强大功能。



### 1.4 Hello World

==代码：ioc_01==

#### 通过各种方式给容器中注册对象

以前是自己new对象，现在所有对象交给容器创建；==给容器中注册组件==



流程：

1. 导包

   ```xml
   <!--Spring核心包-->
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-context</artifactId>
       <version>5.3.6</version>
   </dependency>
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-beans</artifactId>
       <version>5.3.6</version>
   </dependency>
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-core</artifactId>
       <version>5.3.6</version>
   </dependency>
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-expression</artifactId>
       <version>5.3.6</version>
   </dependency>
       
   <!--Spring日志包-->
   <dependency>
       <groupId>commons-logging</groupId>
       <artifactId>commons-logging</artifactId>
       <version>1.2</version>
   </dependency>
   ```

   

2. 写配置

   spring配置文件中，集合了spring的ioc容器管理的所有组件

   创建配置文件

   ```xml
   <!--注册一个person对象,Spring会自动创建这个person对象-->
   <!--一个bean标签可以注册一个组件(对象，类)
   class:写要注册的组件的全类名
   id:这个对象的唯一标识
   name:属性名
   valueL:给属性赋值
   -->
   <bean id="person" class="com.kou.bean.Person">
   <!--使用property给属性赋值        -->
       <property name="lastName" value="Kou"/>
       <property name="gender" value="男"/>
       <property name="email" value="kou1688@foxmail.com"/>
       <property name="age" value="18"/>
   </bean>
   ```

   

3. 测试

   ```java
   /**
    * 从容器中拿到这个组件
    */
   @Test
   public void test1(){
       /*
       ApplicationContext:代表ioc容器
       当前应用的xml配置文件在classpath下
       根据spring配置文件得到ioc容器对象
       */
       ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("ioc.xml");
       //容器帮我们创建好对象了
       Person bean = context.getBean("person",Person.class);
       System.out.println(bean);
   }
   ```

   

两种方式：

+ ```java
  new FileSystemXmlApplicationContext
  ```

  

+ ```java
  new ClassPathXmlApplicationContext
  ```



==单例:==

https://www.runoob.com/design-pattern/singleton-pattern.html



```java
/*
ApplicationContext:代表ioc容器
当前应用的xml配置文件在classpath下
根据spring配置文件得到ioc容器对象
 几个细节:
 1. ApplicationContext（IOC容器的接口）
 2. 组件的创建工作,是由容器完成;
     Person对象是什么时候创建好了？
     容器中对象的创建在容器创建完成的时候就已经创建好了
 3.同一个组件在ioc容器中是单例的;容器启动完成之前都已经创建准备好的
 4.容器中如果没有组件,获取组件?
    org.springframework.beans.factory.NoSuchBeanDefinitionException: No bean named '**' available
 5.ioc容器在创建这个组件对象时,(property)会利用setter方法进行赋值
 6.javaBean的属性名是由什么决定的?
    Getter/Setter方法是属性名
    set去掉后面那后面一串首字母小写就是属性名
    所有的getter/setter都用自动生成的
*/	
```



##### 构造器赋值

第一种（掌握）

```xml
<!--调用有参构造器进行创建对象并赋值-->
<bean id="person03" class="com.kou.bean.Person">
    <constructor-arg name="lastName" value="小花"/>
    <constructor-arg name="gender" value="女"/>
    <constructor-arg name="email" value="111@qq.com"/>
    <constructor-arg name="age" value="18"/>
</bean>
```



第二种（了解）

```xml
<!--省略name属性的构造器注入,按照构造器参数的位置-->
<bean id="person04" class="com.kou.bean.Person">
    <constructor-arg name="age" index="1" value="18"/>
    <constructor-arg name="email" index="3" value="222@qq.com"/>
    <constructor-arg name="lastName" index="0" value="小花"/>
    <constructor-arg name="gender" index="2" value="女"/>
</bean>
```



##### 通过p名称空间为bean赋值

名称空间是用来防止标签重复的

```xml
<!--p名称空间赋值-->
<!--名称空间是用来防止标签重复的-->
<bean id="person06" class="com.kou.bean.Person"
p:age="18" p:email="666@qq.com" p:gender="男" p:lastName="小明">
</bean>
```



##### 正确的为各种类型属性赋值

###### 集合类属性

```xml
<bean id="car01" class="com.kou.bean.Car">
    <property name="carName" value="宝马"/>
    <property name="color" value="绿色"/>
    <property name="price" value="300000"/>
</bean>
<bean id="book01" class="com.kou.bean.Book">
    <property name="bookName" value="东游记"/>
    <property name="author" value="作者"/>
</bean>
<!--实验4:正确的为各种类型属性赋值-->
<bean id="person01" class="com.kou.bean.Person">
    <!--设置null值-->
    <property name="lastName">
        <null/>
    </property>
    <!--设置引用类型值 引用外部Bean-->
    <property name="car" ref="car01"/>
</bean>
<bean id="person02" class="com.kou.bean.Person">
    <!--如何为list赋值-->
    <property name="books">
        <list>
            <!--内部 内部bean id不能被引用-->
            <bean class="com.kou.bean.Book" p:bookName="西游记" p:a
            <!--引用外部-->
            <ref bean="book01"/>
        </list>
    </property>
    <!--Map-->
    <property name="maps">
        <map>
            <!--一个entry代表一个键值对-->
            <entry key="key01" value="张三"/>
            <entry key="key02" value="18"/>
            <entry key="key03" value-ref="book01"/>
            <entry key="key04">
                <bean class="com.kou.bean.Car">
                    <property name="carName" value="宝马"/>
                </bean>
            </entry>
            <!--<entry>
                <map></map>
            </entry>-->
        </map>
    </property>
    <!--properties-->
    <property name="properties">
        <!--所有的k=v都是string-->
        <props>
            <prop key="username">root</prop>
            <prop key="password">1234</prop>
        </props>
    </property>
</bean>
```



```xml
<!--util名称空间创建集合类型的bean-->
<!--相当于new LinkedHashMap<>()-->
<util:map id="myMap">
    <!--添加元素-->
    <entry key="key01" value="张三"/>
    <entry key="key02" value="18"/>
    <entry key="key03" value-ref="book01"/>
    <entry key="key04">
        <bean class="com.kou.bean.Car">
            <property name="carName" value="宝马"/>
        </bean>
    </entry>
</util:map>
<!--
[ [],Person,12,{} ]
-->
<util:list id="myList">
    <list/>
    <bean class="com.kou.bean.Person"/>
    <value/>
    <ref bean="myMap"/>
</util:list>
```



###### 级联属性赋值

属性的属性



```xml
<!--
级联属性赋值
级联属性可以修改属性的属性,注意原来的bean会被修改
-->
<bean id="person4" class="com.kou.bean.Person">
    <!--为car赋值时，改变car的价格-->
    <property name="car" ref="car01"/>
    <!---->
    <property name="car.price" value="90000"/>
</bean>
```



##### bean重用 模板bean bean之间依赖 作用域

通过继承实现bean配置信息的重用

```xml
<bean id="person5" class="com.kou.bean.Person">
    <property name="lastName" value="张三"/>
    <property name="age" value="18"/>
    <property name="gender" value="男"/>
    <property name="email" value="666@kou.com"/>
</bean>
<!--parent:指定当前bean的配置信息继承与哪一个-->
<bean id="person6" parent="person5">
    <property name="lastName" value="李四"/>
</bean>
```



通过abstract属性创建一个模板bean

```xml
<!--abstract="true" 这个bean配置是一个抽象的,不能获取他的实例,只能被别人用来继承-->
<bean id="person5" class="com.kou.bean.Person" abstract="true">
    <property name="lastName" value="张三"/>
    <property name="age" value="18"/>
    <property name="gender" value="男"/>
    <property name="email" value="666@kou.com"/>
</bean>
```



bean之间的依赖

```xml
<!--
原来是按照配置bean顺序创建bean
可以改变bean的创建顺序
实验8:bean之间的依赖（只是改变创建顺序）
-->
<bean id="person" class="com.kou.bean.Person" depends-on="car,book"/>
<bean id="car" class="com.kou.bean.Car"/>
<bean id="book" class="com.kou.bean.Book"/>
```



实验9:测试bean的作用域,分别创建单实例和多实例的bean♥

```xml
<!--
实验9:测试bean的作用域,分别创建单实例和多实例的bean♥
bean作用域:指定bean是否单实例,xxx;默认:单实例
prototype:多实例(原型)
1.容器启动默认不会去创建多实例bean
2.获取的时候创建这个bean
3.每次获取都会创建一个新的对象
singleton:单例(默认)
1.在容器启动完成之前就已经创建好对象，保存在容器中了
2.任何获取都是获取之前创建好的哪个对象
-->
<bean id="book" class="com.kou.bean.Book">
</bean>
```





##### 配置工厂方法创建的bean

实验五:配置通过静态工厂方法创建的bean、实例工厂方法创建的bean、FactoryBean

bean的创建默认就是框架利用反射new出来的bean实例
工厂模式:工厂帮我们创建对象
静态工厂:工厂本身不用创建对象;通过静态方法调用,对象=工厂类.工厂方法名();
实例工厂:工厂本身需要创建对象;
工厂类 工厂对象=new 工厂类();
工厂对象.方法名("");



```xml
<!--1.静态工厂(不需要创建工厂本身) factory-method指定工厂方法-->
<bean id="airPlane01" class="com.kou.factory.AirPlaneStaticFactory" factory-method="getAirPlane">
    <!--可以为方法指定参数-->
    <constructor-arg value="李四"/>
</bean>
```



```xml
<!--2.实例工厂
    先配置出实例工厂对象
    配置我们要创建的AirPlane使用哪个工厂创建
    factory-bean:使用哪个工厂实例
    factory-method:使用哪个工厂方法
-->
<bean id="airPlaneInstanceFactory" class="com.kou.factory.AirPlaneInstanceFactory"/>
<bean id="airPlane02" class="com.kou.bean.AirPlane" factory-bean="airPlaneInstanceFactory"
      factory-method="getAirPlane">
    <constructor-arg value="张三"/>
</bean>
```



###### 实现FactoryBean的工厂

```java
/**
 * 实现了FactoryBean接口的类是Spring可以认识的工厂类
 * Spring会自动地调用工厂方法创建实例
 * 1.编写一个FactoryBean的实现类
 * 2.在spring配置文件中进行注册
 *
 * @author Kou
 * @date: 2021/7/28 15:25
 */
public class MyFactoryBeanImpl implements FactoryBean<Book> {

    /**
     * 是单例?
     *
     * @return false:不是单例;true:是单例
     */
    @Override
    public boolean isSingleton() {
        return false;
    }

    /**
     * 工厂方法;
     *
     * @return 返回创建的对象
     */
    @Override
    public Book getObject() throws Exception {
        Book book = new Book();
        book.setBookName(UUID.randomUUID().toString());
        return book;
    }

    /**
     * Spring会自动调用这个方法来确认创建的对象是什么类型
     *
     * @return 返回创建的对象类型
     */
    @Override
    public Class<?> getObjectType() {
        return Book.class;
    }
}
```

```xml
<!--FactoryBean是Spring规定的一个接口,只要是这个接口的实现类.Spring都认为是一个工厂
ioc容器启动时不会创建工厂实例-->
<bean id="myFactoryBeanImpl" class="com.kou.factory.MyFactoryBeanImpl"/>
```



##### 创建带有生命周期方法的Bean



代码：ioc_02

```xml
<!--
创建带有生命周期方法的bean
生命周期:bean的创建到销毁
    ioc容器中注册的bean：
        1.单例的bean,容器启动的时候就会创建好,容器关闭也会销毁创建的bean
        2.多实例bean,获取的时候才创建;
    我们可以为bean自定义一些生命周期方法;spring在创建或者销毁的时候就会调用指定的方法
    自定义初始化方法和销毁方法:可以抛异常,但不能有参数
-->
<bean id="book01" class="com.kou.bean.Book" init-method="myInit" destroy-method="myDestroy" scope="prototype">
</bean>
```

```java
/**
 * Bean的生命周期
 * 单例:
 * 构造器---->初始化方法---->(容器关闭)销毁方法
 * 多实例:
 * 获取bean(构造器--->初始化方法)---->容器关闭不会调用bean的销毁方法
 */
```





##### Bean的后置处理器

```java
/**
 * 1.编写后置处理器的实现类
 * 2.将后置处理器注册在配置文件中
 *
 * @author Kou
 * @date: 2021/7/28 18:02
 */
public class MyBeanPostProcessor implements BeanPostProcessor {
    /**
     * 初始化之前调用
     *
     * @param bean 将要初始化的bean
     * @return 返回传入的bean
     */
    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println(beanName + " bean将调用初始化方法 " + bean);
        return bean;
    }

    /**
     * 初始化方法之后调用
     *
     * @param bean     初始化的bean
     * @param beanName 在xml中配置的id
     * @return 返回的是什么,容器中保存的就是什么
     */
    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println(beanName + " bean初始化方法调用完了 " + bean);
        return bean;
    }
}
```

```xml
<!--
测试bean的后置处理器
Spring有一个接口:后置处理器:可以在bean的初始化前后调用方法
无论bean是否有初始化方法;后置处理器都会默认其有
-->
<bean id="beanPostProcessor" class="com.kou.bean.MyBeanPostProcessor"/>
```



##### Spring管理连接池



```xml
<!--实验12:引用外部配置文件*-->
<!--数据库连接池作为单实例是最好的;一个项目就一个连接池,连接池里面管理很多连接-->
<!--可以让Spring帮我们创建连接池对象,（管理连接池）-->
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="user" value="root"/>
    <property name="password" value="1234"/>
    <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/jdbcstudy?
    serverTimezone=GMT%2B8&amp;useSSL=false&amp;useUnicode=true&amp;characterEncoding=utf8"/>
    <property name="driverClass" value="com.mysql.cj.jdbc.Driver"/>
</bean>
```

测试：

```java
/**
 * 从容器中拿到连接池
 */
@Test
public void test02() throws SQLException {
    //按照类型获取，可以获取这个类型下的所有类子类等等
    DataSource bean = ioc.getBean("dataSource",DataSource.class);
    System.out.println(bean.getConnection());
}
```



引用外部文件：

```xml
<!--实验12:引用外部配置文件*,依赖Context名称空间-->
<!--数据库连接池作为单实例是最好的;一个项目就一个连接池,连接池里面管理很多连接-->
<!--可以让Spring帮我们创建连接池对象,（管理连接池）-->
<!--加载外部配置文件  classpath:表示引用类路径下的文件-->
<context:property-placeholder location="classpath:dbconfig.properties"/>
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="user" value="${jdbc.username}"/>
    <property name="password" value="${jdbc.password}"/>
    <property name="jdbcUrl" value="${jdbc.Url}"/>
    <property name="driverClass" value="${jdbc.driverClass}"/>
</bean>
```



##### SPEL测试

spring expression language   spring表达式语言

```xml
<!--SPel测试-->
<bean id="person04" class="com.kou.bean.Person">
    <!--字面量-->
    <property name="age" value="#{12345*6}"/>
    <!--引用其他bean的属性值-->
    <property name="lastName" value="#{book01.bookName}"/>
    <property name="car" value="#{car}"/>
    <!--调用静态方法-->
    <property name="email" value="#{T(java.util.UUID).randomUUID().toString()}"/>
    <!--调用非静态方法-->
    <property name="gender" value="#{book01.getBookName()}"/>
</bean>
```





### 1.5 基于XML的自动装配（自定义类型自动赋值）

自定义类型的属性是一个对象，这个对象在容器中可能存在

```xml
<bean id="car" class="com.kou.bean.Car">
    <property name="carName" value="宝马"/>
    <property name="color" value="白色"/>
</bean>
<bean id="book01" class="com.kou.bean.Book">
    <property name="bookName" value="book1"/>
</bean>
<bean id="book02" class="com.kou.bean.Book">
    <property name="bookName" value="book2"/>
</bean>
<bean id="book03" class="com.kou.bean.Book">
    <property name="bookName" value="book3"/>
</bean>

<!--自动赋值(自动装配):仅限于对自定义类型有效
    按照某种规则自动装配
    autowire="byName"
    按照名字:private Car car;
    1.以属性名作为id去容器中找到这个组件,给它赋值。如果找不到就装配null
    autowire="byType"
    1.按照属性类型作为查找依据去容器中找到这个组件;
    ioc.getBean(Car.class)
    2.如果容器中有多个这种类型的组件,报错
    3.如果没找到,装配null
    按照构造器:autowire="constructor"
    1.先按照有参构造器参数的类型进行装配,没有就直接为组件装配null
    2.如果按照类型找到多个;参数名作为id继续匹配
    3.不会报错
自动为属性赋值
-->
<bean id="person" class="com.kou.bean.Person" autowire="byType"/>
```



