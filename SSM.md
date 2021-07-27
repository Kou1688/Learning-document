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

