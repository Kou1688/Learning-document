[TOC]



# SpringBoot核心技术



## spring与springboot背景

https://www.yuque.com/atguigu/springboot



### 微服务

- 微服务是一种架构风格
- 一个应用拆分为一组小型服务
- 每个服务运行在自己的进程内，也就是可独立部署和升级
- 服务之间使用轻量级HTTP交互
- 服务围绕业务功能拆分
- 可以由全自动部署机制独立部署
- 去中心化，服务自治。服务可以使用不同的语言、不同的存储技术





## 基础入门



### HelloWorld

+ 导入依赖

  ```xml
  <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.5.4</version>
  </parent>
  
  <dependencies>
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-web</artifactId>
      </dependency>
  </dependencies>
  
  
  <build>
      <plugins>
          <plugin>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-maven-plugin</artifactId>
          </plugin>
      </plugins>
  </build>
  ```



+ 主程序

  ```java
  /**
   * -@SpringBootApplication 告诉SpringBoot这是一个SpringBoot应用
   * 主程序类
   *
   * @author Kou
   * @date: 2021/9/5 10:53
   */
  @SpringBootApplication
  public class MainApplication {
      public static void main(String[] args) {
          SpringApplication.run(MainApplication.class, args);
      }
  }
  ```

  

+ 编写业务

  ```java
  /**
   * -@RestController:相当于@ResponseBody@RequestMapping
   *
   * @author Kou
   * @date: 2021/9/5 10:56
   */
  @RestController
  public class HelloController {
  
      @RequestMapping("/hello")
      public String handle01() {
          return "Hello,SpringBoot!";
      }
  }
  ```



+ 测试

  直接运行main方法



+ 简化配置

  application.properties

  ```properties
  #相当于更改了Tomcat的端口号
  server.port=8888
  ```



+ 简化部署

  ```xml
  <build>
      <plugins>
          <plugin>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-maven-plugin</artifactId>
          </plugin>
      </plugins>
  </build>
  ```

  把项目打成jar包，直接在目标服务器执行即可

  

+ 注意点

  + 取消掉cmd的快速编辑模式

  





### 依赖管理特性

+ 父项目做依赖管理

  ```xml
  依赖管理
  <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.5.4</version>
  </parent>
  
  他的父项目
  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.5.4</version>
  </parent>
  
  几乎声明了所有开发中常用的依赖的版本号，自动版本仲裁机制
  ```

+ 无需关注版本号，自动版本仲裁

  1. 引入依赖默认都不需要写版本号
  2. 引入非版本仲裁的jar，要写版本号

+ 可以修改默认的版本号

  ```xml
  1、查看spring-boot-dependencies里面规定当前依赖的版本 用的 key。
  2、在当前项目里面重写配置
  <properties>
      <mysql.version>5.1.43</mysql.version>
  </properties>
  ```

+ starter场景启动器

  ```xml
  1.spring-boot-starter-*   *就是某种场景
  2.只要引入starter，这个场景的所有常规需要的依赖全部会自动引入
  3.SpringBoot支持的所有场景
  https://docs.spring.io/spring-boot/docs/current/reference/html/using.html#using.build-systems.starters
  4.见到的 *-spring-boot-starter 是第三方提供的场景启动器
  5.所有场景启动器最底层的依赖
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
    <version>2.5.4</version>
    <scope>compile</scope>
  </dependency>
  ```

  



### 自动配置特性

- 自动配好Tomcat

- - 引入Tomcat依赖。
  - 配置Tomcat

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-tomcat</artifactId>
    <version>2.5.4</version>
    <scope>compile</scope>
</dependency>
```

- 自动配好SpringMVC

- - 引入SpringMVC全套组件
  - 自动配好SpringMVC常用组件（功能）

- 自动配好Web常见功能，如：字符编码问题

- - SpringBoot帮我们配置好了所有web开发的常见场景

- 默认的包结构

- - ==主程序所在包及其下面的所有子包==里面的组件都会被默认扫描进来
  - 无需以前的包扫描配置

- - 想要改变扫描路径，@SpringBootApplication(scanBasePackages=**"com.kou"**)

- - - 或者@ComponentScan 指定扫描路径

```java
@SpringBootApplication
等同于
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan("com.kou.boot")
```

- 各种配置拥有默认值

- - 默认配置最终都是映射到某个类上，如：MultipartProperties
  - 配置文件的值最终会绑定每个类上，这个类会在容器中创建对象

- 按需加载所有自动配置项

- - 非常多的starter
  - 引入了哪些场景这个场景的自动配置才会开启

- - SpringBoot所有的自动配置功能都在 spring-boot-autoconfigure 包里面





### 容器功能

#### 组件添加



##### 1.@Configuration

+ 基本使用

+ **Full模式与Lite模式**

  + 示例

  + 最佳实战

    + ==配置类组件之间无依赖关系用Lite模式加速容器启动过程，减少判断==

      Lite模式的`@Bean`方法**不能声明Bean之间的依赖关系**。因此，这样的`@Bean`方法**不应该调用其他@Bean方法**。每个这样的方法实际上**只是一个特定Bean引用的工厂方法(factory-method)**，没有任何特殊的运行时语义。

    + ==配置类组件之间有依赖关系，方法会被调用得到之前单实例组件，用Full模式==

  ```java
  /**
   * -@Configuration告诉SpringBoot这是一个配置类
   * 1.容器中的组件默认是单实例的
   * 2.配置类本身也是一个组件
   * 3.proxyBeanMethods:代理bean的方法
   * Full(proxyBeanMethods = true)【保证每个@Bean方法被调用多少次返回的组件都是单实例的】
   * Lite(proxyBeanMethods = false)【每个@Bean方法被调用多少次返回的组件都是新创建的】
   * 组件依赖
   * 全模式与轻量级模式
   *
   * @author Kou
   * @date: 2021/9/5 15:55
   */
  @Configuration(proxyBeanMethods = true)
  public class MyConfig {
  
      /**
       * Bean注解
       * 给容器中添加组件。以方法名作为组件的id。
       * 外部无论对配置类的这个组件注册方法调用多少次,获取的都是之前注册容器中的单实例对象
       *
       * @return 返回类型就是组件类型。返回的值，就是组件在容器中的实例。
       */
      @Bean
      public User user01() {
          User zhangsan = new User("张三", 18);
          zhangsan.setPet(pet01());
          return zhangsan;
      }
  
      @Bean("jerry")
      public Pet pet01() {
          return new Pet("jerry");
      }
  }
  
  
  
  ---------------------------------------
  /**
   * -@SpringBootApplication 告诉SpringBoot这是一个SpringBoot应用
   * 主程序类
   *
   * @author Kou
   * @date: 2021/9/5 10:53
   */
  @SpringBootApplication
  public class MainApplication {
      public static void main(String[] args) {
          //1.返回IOC容器
          ConfigurableApplicationContext run = SpringApplication.run(MainApplication.class, args);
  
          //2.查看容器内的组件
          String[] beanDefinitionNames = run.getBeanDefinitionNames();
          for (String name : beanDefinitionNames) {
              System.out.println(name);
          }
  
          MyConfig bean = run.getBean(MyConfig.class);
          System.out.println(bean);
  
          /*
          @Configuration(proxyBeanMethods = true)代理对象调用方法,SpringBoot总会检查这个组件是否在容器内
          保持组件单实例
          */
          User user = bean.user01();
          User user1 = bean.user01();
          System.out.println(user == user1);
  
  
          User user01 = run.getBean("user01", User.class);
          Pet jerry = run.getBean("jerry", Pet.class);
  
          System.out.println("用户的宠物:" + (user01.getPet() == jerry));
      }
  }
  ```



##### 2.@Import

```java
/** 
* 4、@Import({User.class, DBHelper.class})
 *     给容器中自动创建出这两个类型的组件、默认组件的名字就是全类名
 *
 *
 *
 */

@Import({User.class, DBHelper.class})
@Configuration(proxyBeanMethods = false) //告诉SpringBoot这是一个配置类 == 配置文件
public class MyConfig {
}
```

@Import 高级用法： https://www.bilibili.com/video/BV1gW411W7wy?p=8



##### 3.@Conditional

条件装配：满足Conditional指定的条件，则进行组件注入

```java
=====================测试条件装配==========================
@Configuration(proxyBeanMethods = false) //告诉SpringBoot这是一个配置类 == 配置文件
    
//@ConditionalOnBean(name = "tom")
@ConditionalOnMissingBean(name = "tom")
    
public class MyConfig {


    /**
     * Full:外部无论对配置类中的这个组件注册方法调用多少次获取的都是之前注册容器中的单实例对象
     * @return
     */

    @Bean //给容器中添加组件。以方法名作为组件的id。返回类型就是组件类型。返回的值，就是组件在容器中的实例
    public User user01(){
        User zhangsan = new User("zhangsan", 18);
        //user组件依赖了Pet组件
        zhangsan.setPet(tomcatPet());
        return zhangsan;
    }

    @Bean("tom22")
    public Pet tomcatPet(){
        return new Pet("tomcat");
    }
}

public static void main(String[] args) {
    //1、返回我们IOC容器
    ConfigurableApplicationContext run = SpringApplication.run(MainApplication.class, args);

    //2、查看容器里面的组件
    String[] names = run.getBeanDefinitionNames();
    for (String name : names) {
        System.out.println(name);
    }

    boolean tom = run.containsBean("tom");
    System.out.println("容器中Tom组件："+tom);

    boolean user01 = run.containsBean("user01");
    System.out.println("容器中user01组件："+user01);

    boolean tom22 = run.containsBean("tom22");
    System.out.println("容器中tom22组件："+tom22);


}
```





##### 4.@ImportResource

标在一个配置类上

```java
@ImportResource(locations = "classpath:beans.xml")
```



##### 5.@ConfigurationProperties

配置绑定

###### @Component + @ConfigurationProperties

```properties
mycar.brand=BYD
mycar.price=100000
```



```java
/**
 * 只有在容器中的组件，才会拥有SpringBoot提供的强大功能
 */
@Component
@ConfigurationProperties(prefix = "mycar")
public class Car {

    private String brand;
    private Integer price;

}
```



###### @EnableConfigurationProperties + @ConfigurationProperties

```java
@EnableConfigurationProperties(Car.class)
//1、开启Car配置绑定功能
//2、把这个Car这个组件自动注册到容器中
public class MyConfig {
}
```

