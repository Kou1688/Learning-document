[TOC]







# spring与springboot背景

https://www.yuque.com/atguigu/springboot



# 微服务

- 微服务是一种架构风格
- 一个应用拆分为一组小型服务
- 每个服务运行在自己的进程内，也就是可独立部署和升级
- 服务之间使用轻量级HTTP交互
- 服务围绕业务功能拆分
- 可以由全自动部署机制独立部署
- 去中心化，服务自治。服务可以使用不同的语言、不同的存储技术





# 基础入门



## HelloWorld

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
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-configuration-processor</artifactId>
          <optional>true</optional>
      </dependency>
  
  </dependencies>
  
  
  <build>
      <plugins>
          <plugin>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-maven-plugin</artifactId>
              <configuration>
                      <excludes>
                          <exclude>
                              <groupId>org.springframework.boot</groupId>
                              <artifactId>spring-boot-configuration-processor</artifactId>
                          </exclude>
                      </excludes>
                  </configuration>
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

  





## 依赖管理特性

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

  



## 自动配置特性

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





## 容器功能

### 组件添加



#### 1.@Configuration

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



#### 2.@Import

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



#### 3.@Conditional

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





#### 4.@ImportResource

标在一个配置类上

```java
@ImportResource(locations = "classpath:beans.xml")
```



#### 5.@ConfigurationProperties

配置绑定

##### @Component + @ConfigurationProperties

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



##### @EnableConfigurationProperties + @ConfigurationProperties

```java
@EnableConfigurationProperties(Car.class)
//1、开启Car配置绑定功能
//2、把这个Car这个组件自动注册到容器中
public class MyConfig {
}
@ConfigurationProperties(prefix = "mycar")
public class Car {
```





## 自动配置原理入门（源码分析）

### 引导加载自动配置类

```java
//@SpringBootApplication
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
      @Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication 
    
    
```





### @SpringBootConfiguration

本质是一个`@Configuration`，代表当前是一个配置类





### @ComponentScan

指定扫描哪些





### ==@EnableAutoConfiguration==

```java
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration
```





#### 1.@AutoConfigurationPackage

自动配置包？

**指定了默认的包规则**

```java
@Import(AutoConfigurationPackages.Registrar.class) //给容器中导入一个组件
public @interface AutoConfigurationPackage
    
//利用Registrar给容器中导入一系列组件
    
//将指定的一个包下的所有组件导入进来  main程序所在包
```



#### 2、@Import(AutoConfigurationImportSelector.class)

```java
1.利用 getAutoConfigurationEntry(annotationMetadata);给容器中批量导入一些组件
2.调用List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes)获取到所有需要导入到容器的配置类
3.利用工厂加载 Map<String, List<String>> loadSpringFactories(ClassLoader classLosader)；得到所有组件
4.从META-INF/spring.factories来加载一个文件
    默认扫描我们当前系统里面所有META-INF/spring.factories位置的文件
    spring-boot-autoconfigure-2.5.4.jar包里面也有META-INF/spring.factories
```



![image-20210906151050291](SpringBoot.assets/image-20210906151050291.png)



```properties
spring-boot-autoconfigure-2.5.4.jar包里面也有META-INF/spring.factories
文件里面写死了,springboot一启动就要给容器中加载的所有配置
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\
org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration,\
org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration,\
org.springframework.boot.autoconfigure.context.ConfigurationPropertiesAutoConfiguration,\
org.springframework.boot.autoconfigure.context.LifecycleAutoConfiguration,\
org.springframework.boot.autoconfigure.context.MessageSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.context.PropertyPlaceholderAutoConfiguration,\
org.springframework.boot.autoconfigure.couchbase.CouchbaseAutoConfiguration,\
org.springframework.boot.autoconfigure.dao.PersistenceExceptionTranslationAutoConfiguration,\
org.springframework.boot.autoconfigure.data.cassandra.CassandraDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.cassandra.CassandraReactiveDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.cassandra.CassandraReactiveRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.cassandra.CassandraRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.couchbase.CouchbaseDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.couchbase.CouchbaseReactiveDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.couchbase.CouchbaseReactiveRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.couchbase.CouchbaseRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.elasticsearch.ReactiveElasticsearchRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.elasticsearch.ReactiveElasticsearchRestClientAutoConfiguration,\
org.springframework.boot.autoconfigure.data.jdbc.JdbcRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.jpa.JpaRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.ldap.LdapRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.mongo.MongoDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.mongo.MongoReactiveDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.mongo.MongoReactiveRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.mongo.MongoRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.neo4j.Neo4jDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.neo4j.Neo4jReactiveDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.neo4j.Neo4jReactiveRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.neo4j.Neo4jRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.r2dbc.R2dbcDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.r2dbc.R2dbcRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.redis.RedisAutoConfiguration,\
org.springframework.boot.autoconfigure.data.redis.RedisReactiveAutoConfiguration,\
org.springframework.boot.autoconfigure.data.redis.RedisRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.rest.RepositoryRestMvcAutoConfiguration,\
org.springframework.boot.autoconfigure.data.web.SpringDataWebAutoConfiguration,\
org.springframework.boot.autoconfigure.elasticsearch.ElasticsearchRestClientAutoConfiguration,\
org.springframework.boot.autoconfigure.flyway.FlywayAutoConfiguration,\
org.springframework.boot.autoconfigure.freemarker.FreeMarkerAutoConfiguration,\
org.springframework.boot.autoconfigure.groovy.template.GroovyTemplateAutoConfiguration,\
org.springframework.boot.autoconfigure.gson.GsonAutoConfiguration,\
org.springframework.boot.autoconfigure.h2.H2ConsoleAutoConfiguration,\
org.springframework.boot.autoconfigure.hateoas.HypermediaAutoConfiguration,\
org.springframework.boot.autoconfigure.hazelcast.HazelcastAutoConfiguration,\
org.springframework.boot.autoconfigure.hazelcast.HazelcastJpaDependencyAutoConfiguration,\
org.springframework.boot.autoconfigure.http.HttpMessageConvertersAutoConfiguration,\
org.springframework.boot.autoconfigure.http.codec.CodecsAutoConfiguration,\
org.springframework.boot.autoconfigure.influx.InfluxDbAutoConfiguration,\
org.springframework.boot.autoconfigure.info.ProjectInfoAutoConfiguration,\
org.springframework.boot.autoconfigure.integration.IntegrationAutoConfiguration,\
org.springframework.boot.autoconfigure.jackson.JacksonAutoConfiguration,\
org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.jdbc.JdbcTemplateAutoConfiguration,\
org.springframework.boot.autoconfigure.jdbc.JndiDataSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.jdbc.XADataSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.jdbc.DataSourceTransactionManagerAutoConfiguration,\
org.springframework.boot.autoconfigure.jms.JmsAutoConfiguration,\
org.springframework.boot.autoconfigure.jmx.JmxAutoConfiguration,\
org.springframework.boot.autoconfigure.jms.JndiConnectionFactoryAutoConfiguration,\
org.springframework.boot.autoconfigure.jms.activemq.ActiveMQAutoConfiguration,\
org.springframework.boot.autoconfigure.jms.artemis.ArtemisAutoConfiguration,\
org.springframework.boot.autoconfigure.jersey.JerseyAutoConfiguration,\
org.springframework.boot.autoconfigure.jooq.JooqAutoConfiguration,\
org.springframework.boot.autoconfigure.jsonb.JsonbAutoConfiguration,\
org.springframework.boot.autoconfigure.kafka.KafkaAutoConfiguration,\
org.springframework.boot.autoconfigure.availability.ApplicationAvailabilityAutoConfiguration,\
org.springframework.boot.autoconfigure.ldap.embedded.EmbeddedLdapAutoConfiguration,\
org.springframework.boot.autoconfigure.ldap.LdapAutoConfiguration,\
org.springframework.boot.autoconfigure.liquibase.LiquibaseAutoConfiguration,\
org.springframework.boot.autoconfigure.mail.MailSenderAutoConfiguration,\
org.springframework.boot.autoconfigure.mail.MailSenderValidatorAutoConfiguration,\
org.springframework.boot.autoconfigure.mongo.embedded.EmbeddedMongoAutoConfiguration,\
org.springframework.boot.autoconfigure.mongo.MongoAutoConfiguration,\
org.springframework.boot.autoconfigure.mongo.MongoReactiveAutoConfiguration,\
org.springframework.boot.autoconfigure.mustache.MustacheAutoConfiguration,\
org.springframework.boot.autoconfigure.neo4j.Neo4jAutoConfiguration,\
org.springframework.boot.autoconfigure.netty.NettyAutoConfiguration,\
org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaAutoConfiguration,\
org.springframework.boot.autoconfigure.quartz.QuartzAutoConfiguration,\
org.springframework.boot.autoconfigure.r2dbc.R2dbcAutoConfiguration,\
org.springframework.boot.autoconfigure.r2dbc.R2dbcTransactionManagerAutoConfiguration,\
org.springframework.boot.autoconfigure.rsocket.RSocketMessagingAutoConfiguration,\
org.springframework.boot.autoconfigure.rsocket.RSocketRequesterAutoConfiguration,\
org.springframework.boot.autoconfigure.rsocket.RSocketServerAutoConfiguration,\
org.springframework.boot.autoconfigure.rsocket.RSocketStrategiesAutoConfiguration,\
org.springframework.boot.autoconfigure.security.servlet.SecurityAutoConfiguration,\
org.springframework.boot.autoconfigure.security.servlet.UserDetailsServiceAutoConfiguration,\
org.springframework.boot.autoconfigure.security.servlet.SecurityFilterAutoConfiguration,\
org.springframework.boot.autoconfigure.security.reactive.ReactiveSecurityAutoConfiguration,\
org.springframework.boot.autoconfigure.security.reactive.ReactiveUserDetailsServiceAutoConfiguration,\
org.springframework.boot.autoconfigure.security.rsocket.RSocketSecurityAutoConfiguration,\
org.springframework.boot.autoconfigure.security.saml2.Saml2RelyingPartyAutoConfiguration,\
org.springframework.boot.autoconfigure.sendgrid.SendGridAutoConfiguration,\
org.springframework.boot.autoconfigure.session.SessionAutoConfiguration,\
org.springframework.boot.autoconfigure.security.oauth2.client.servlet.OAuth2ClientAutoConfiguration,\
org.springframework.boot.autoconfigure.security.oauth2.client.reactive.ReactiveOAuth2ClientAutoConfiguration,\
org.springframework.boot.autoconfigure.security.oauth2.resource.servlet.OAuth2ResourceServerAutoConfiguration,\
org.springframework.boot.autoconfigure.security.oauth2.resource.reactive.ReactiveOAuth2ResourceServerAutoConfiguration,\
org.springframework.boot.autoconfigure.solr.SolrAutoConfiguration,\
org.springframework.boot.autoconfigure.sql.init.SqlInitializationAutoConfiguration,\
org.springframework.boot.autoconfigure.task.TaskExecutionAutoConfiguration,\
org.springframework.boot.autoconfigure.task.TaskSchedulingAutoConfiguration,\
org.springframework.boot.autoconfigure.thymeleaf.ThymeleafAutoConfiguration,\
org.springframework.boot.autoconfigure.transaction.TransactionAutoConfiguration,\
org.springframework.boot.autoconfigure.transaction.jta.JtaAutoConfiguration,\
org.springframework.boot.autoconfigure.validation.ValidationAutoConfiguration,\
org.springframework.boot.autoconfigure.web.client.RestTemplateAutoConfiguration,\
org.springframework.boot.autoconfigure.web.embedded.EmbeddedWebServerFactoryCustomizerAutoConfiguration,\
org.springframework.boot.autoconfigure.web.reactive.HttpHandlerAutoConfiguration,\
org.springframework.boot.autoconfigure.web.reactive.ReactiveWebServerFactoryAutoConfiguration,\
org.springframework.boot.autoconfigure.web.reactive.WebFluxAutoConfiguration,\
org.springframework.boot.autoconfigure.web.reactive.error.ErrorWebFluxAutoConfiguration,\
org.springframework.boot.autoconfigure.web.reactive.function.client.ClientHttpConnectorAutoConfiguration,\
org.springframework.boot.autoconfigure.web.reactive.function.client.WebClientAutoConfiguration,\
org.springframework.boot.autoconfigure.web.servlet.DispatcherServletAutoConfiguration,\
org.springframework.boot.autoconfigure.web.servlet.ServletWebServerFactoryAutoConfiguration,\
org.springframework.boot.autoconfigure.web.servlet.error.ErrorMvcAutoConfiguration,\
org.springframework.boot.autoconfigure.web.servlet.HttpEncodingAutoConfiguration,\
org.springframework.boot.autoconfigure.web.servlet.MultipartAutoConfiguration,\
org.springframework.boot.autoconfigure.web.servlet.WebMvcAutoConfiguration,\
org.springframework.boot.autoconfigure.websocket.reactive.WebSocketReactiveAutoConfiguration,\
org.springframework.boot.autoconfigure.websocket.servlet.WebSocketServletAutoConfiguration,\
org.springframework.boot.autoconfigure.websocket.servlet.WebSocketMessagingAutoConfiguration,\
org.springframework.boot.autoconfigure.webservices.WebServicesAutoConfiguration,\
org.springframework.boot.autoconfigure.webservices.client.WebServiceTemplateAutoConfiguration





```





### 按需开启自动配置项

```properties
虽然131个场景的所有配置，启动的时候默认全部加载。
按照条件装配规则（@Conditional）,最终会按需配置
```





### 修改默认配置

```java
@Bean
@ConditionalOnBean(MultipartResolver.class)  //容器中有这个类型组件
@ConditionalOnMissingBean(name = DispatcherServlet.MULTIPART_RESOLVER_BEAN_NAME) //容器中没有这个名字 multipartResolver 的组件
public MultipartResolver multipartResolver(MultipartResolver resolver) {
    //给@Bean标注的方法传入了对象参数，这个参数的值就会从容器中找。
    //SpringMVC multipartResolver。防止有些用户配置的文件上传解析器不符合规范
    // Detect if the user has created a MultipartResolver but named it incorrectly
    return resolver;
}
给容器中加入了文件上传解析器；
```





SpringBoot默认会在底层配好所有的组件，但是如果用户配置了会以用户的优先

```java
@Bean
	@ConditionalOnMissingBean
	public CharacterEncodingFilter characterEncodingFilter() {
    }
```





总结：

+ SpringBoot先加载所有的自动配置类    xxxxxAutoConfiguration
+ 每个自动配置类按照条件进行生效，默认都会绑定配置文件指定的值，xxxxProperties里面拿。xxxxProperties和配置文件进行了绑定
+ 生效的配置类就会给容器中装配很多的组件
+ 只要容器中有这些组件相当于这些功能就有了
+ 只要用户有自己配置的就以用户的优先
+ 定制化配置
  + 用户直接@Bean替换底层的组件
  + 用户去看这个组件是获取的配置文件什么值就去修改

**xxxxxAutoConfiguration ---> 组件  --->** **xxxxProperties里面拿值  ----> application.properties**



### 最佳实践

+ 引入场景依赖

+ 查看自动配置了哪些（选做）

  + 自己分析，引入场景对应的自动配置一般都生效了

  + ```properties
    配置文件中   debug=true        开启自动配置报告
    ```

+ 是否需要修改

  + 参照文档修改配置项
  + 自定义加入或者替换组件
  + ........
  + 自定义加入或者替换组件
    + @Bean/@Componet
  + 自定义器XXXXCustomizer
  + .........





#### Spring Initailizr（项目初始化向导）





# 核心功能



## 配置文件



### 文件类型



#### 1.1、properties

同以前的properties用法



#### 1.2、yaml

YAML 是 "YAML Ain't Markup Language"（YAML 不是一种标记语言）的递归缩写。在开发的这种语言时，YAML 的意思其实是："Yet Another Markup Language"（仍是一种标记语言）。 



非常适合用来做以数据为中心的配置文件



##### 基本语法

- key: value；kv之间有空格
- 大小写敏感

- 使用缩进表示层级关系
- 缩进不允许使用tab，只允许空格

- 缩进的空格数不重要，只要相同层级的元素左对齐即可
- '#'表示注释

- 字符串无需加引号，如果要加，''与""表示字符串内容 会被 转义/不转义



##### 数据类型

- 字面量：单个的、不可再分的值。date、boolean、string、number、null

  ```yaml
  k: v
  ```

- 对象：键值对的集合。map、hash、set、object 

  ```yaml
  行内写法：  k: {k1:v1,k2:v2,k3:v3}
  #或
  k: 
    k1: v1
    k2: v2
    k3: v3
  ```

- 数组：一组按次序排列的值。array、list、queue

  ```yaml
  行内写法：  k: [v1,v2,v3]
  #或者
  k:
   - v1
   - v2
   - v3
  ```



##### 示例

```java
@Component
@ConfigurationProperties(prefix = "person")
public class Person {
    private String userName;
    private Boolean boss;
    private Date birth;
    private Integer age;
    private Pet pet;
    private String[] interests;
    private List<String> animal;
    private Map<String, Object> score;
    private Set<Double> salarys;
    private Map<String, List<Pet>> allPets;
    
    
    
public class Pet {
    private String name;
    private Double weight;
```

```yaml
person:
  #单引号会将\n作为字符串输出,双引号会将\n作为换行输出
  #双引号不会转义,单引号会转义
  userName: 'zhangsan \n lisi'
  boss: true
  birth: 2021/9/7
  age: 18
  #  interests: [篮球,足球]
  interests:
    - 篮球
    - 足球
  animal: [ 阿猫,阿狗 ]
  score: { english:80,math:90 }
  salarys: [ 9999.98,9999.99 ]
  pet:
    name: 阿狗
    weight: 99.99

  allPets:
    sick:
      - { name: 阿狗,weight: 99.99 }
      - name: 阿猫
        weight: 88.88
      - name: 阿虫
        weight: 77.77
    health:
      - { name: 阿花,weight: 199.99 }
      - { name: 阿明,weight: 199.99 }
```







#### 配置提示

自定义的类和配置文件绑定一般没有提示。

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>


 <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.springframework.boot</groupId>
                            <artifactId>spring-boot-configuration-processor</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
        </plugins>
    </build>
```





## Web开发



### 1.SpringMVC自动配置概览

Spring Boot provides auto-configuration for Spring MVC that **works well with most applications.(大多场景我们都无需自定义配置)**

The auto-configuration adds the following features on top of Spring’s defaults:

- Inclusion of `ContentNegotiatingViewResolver` and `BeanNameViewResolver` beans.

- - 内容协商视图解析器和BeanName视图解析器

- Support for serving static resources, including support for WebJars (covered [later in this document](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-mvc-static-content))).

- - 静态资源（包括webjars）

- Automatic registration of `Converter`, `GenericConverter`, and `Formatter` beans.

- - 自动注册 `Converter，GenericConverter，Formatter `

- Support for `HttpMessageConverters` (covered [later in this document](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-mvc-message-converters)).

- - 支持 `HttpMessageConverters` （后来我们配合内容协商理解原理）

- Automatic registration of `MessageCodesResolver` (covered [later in this document](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-message-codes)).

- - 自动注册 `MessageCodesResolver` （国际化用）

- Static `index.html` support.

- - 静态index.html 页支持

- Custom `Favicon` support (covered [later in this document](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-mvc-favicon)).

- - 自定义 `Favicon`  

- Automatic use of a `ConfigurableWebBindingInitializer` bean (covered [later in this document](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-mvc-web-binding-initializer)).

- - 自动使用 `ConfigurableWebBindingInitializer` ，（DataBinder负责将请求数据绑定到JavaBean上）

> If you want to keep those Spring Boot MVC customizations and make more [MVC customizations](https://docs.spring.io/spring/docs/5.2.9.RELEASE/spring-framework-reference/web.html#mvc) (interceptors, formatters, view controllers, and other features), you can add your own `@Configuration` class of type `WebMvcConfigurer` but **without** `@EnableWebMvc`.

> **不用@EnableWebMvc注解。使用** `**@Configuration**` **+** `WebMvcConfigurer` **自定义规则**



> If you want to provide custom instances of `RequestMappingHandlerMapping`, `RequestMappingHandlerAdapter`, or `ExceptionHandlerExceptionResolver`, and still keep the Spring Boot MVC customizations, you can declare a bean of type `WebMvcRegistrations` and use it to provide custom instances of those components.

> **声明** `**WebMvcRegistrations**` **改变默认底层组件**



> If you want to take complete control of Spring MVC, you can add your own `@Configuration` annotated with `@EnableWebMvc`, or alternatively add your own `@Configuration`-annotated `DelegatingWebMvcConfiguration` as described in the Javadoc of `@EnableWebMvc`.

> **使用** `**@EnableWebMvc+@Configuration+DelegatingWebMvcConfiguration 全面接管SpringMVC**`





### 2.简单功能分析



**代码：boot-05-web-01**



#### 2.1 静态资源访问

##### 1.静态资源目录

只要静态资源放在类路径下： `/static` (or `/public` or `/resources` or `/META-INF/resources`

访问 ： 当前项目根路径/ + 静态资源名 



原理： 静态映射/**。

请求进来，先去找Controller看能不能处理。不能处理的所有请求又都交给静态资源处理器。静态资源也找不到则响应404页面



##### 2.静态资源访问前缀

默认无前缀。

配置之后：

```yaml
spring:
  mvc:
    static-path-pattern: /res/**
```

当前项目 + static-path + 静态资源名 = 静态资源文件夹下找



##### 3.改变静态资源路径

```yaml
spring:
  web:
    resources:
      static-locations: classpath:/haha/
```



##### 4.webjar

自动映射 /[webjars](http://localhost:8080/webjars/jquery/3.5.1/jquery.js)/**

https://www.webjars.org/

```xml
        <dependency>
            <groupId>org.webjars</groupId>
            <artifactId>jquery</artifactId>
            <version>3.5.1</version>
        </dependency>
```

访问地址：[http://localhost:8080/webjars/**jquery/3.5.1/jquery.js**](http://localhost:8080/webjars/jquery/3.5.1/jquery.js)   后面地址要按照依赖里面的包路径





#### 2.2 欢迎页支持

+ 静态资源路径下index.html

  + 可以配置静态资源路径
  + 但是不可以配置静态资源的访问前缀，否则导致index.html不能被默认访问

  ```yaml
  spring:
  #  mvc:
  #    static-path-pattern: /res/**   这个会导致welcome page功能失效
  
    resources:
      static-locations: [classpath:/haha/]
  ```

  

+ controller能处理/index

  + 





#### 2.3 自定义Favicon

favicon.ico 放在静态资源目录下即可。

YAML

复制代码

```yaml
spring:
#  mvc:
#    static-path-pattern: /res/**   这个会导致 Favicon 功能失效
```





#### 2.4 静态资源配置原理（源码分析）

+ SpringBoot启动默认加载  xxxAutoConfiguration类（自动配置类）

+ SpringMVC功能的自动配置类WebMvcAutoConfiguration，生效

  ```java
  @Configuration(proxyBeanMethods = false)
  @ConditionalOnWebApplication(type = Type.SERVLET)
  @ConditionalOnClass({ Servlet.class, DispatcherServlet.class, WebMvcConfigurer.class })
  @ConditionalOnMissingBean(WebMvcConfigurationSupport.class)
  @AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE + 10)
  @AutoConfigureAfter({ DispatcherServletAutoConfiguration.class, TaskExecutionAutoConfiguration.class,
  		ValidationAutoConfiguration.class })
  public class WebMvcAutoConfiguration
  ```

+ 给容器中配了什么

  ```java
  @Configuration(proxyBeanMethods = false)
  @Import(EnableWebMvcConfiguration.class)
  @EnableConfigurationProperties({ WebMvcProperties.class,
        org.springframework.boot.autoconfigure.web.ResourceProperties.class, WebProperties.class })
  @Order(0)
  public static class WebMvcAutoConfigurationAdapter implements WebMvcConfigurer, ServletContextAware
  ```

+ 配置文件的相关的属性和xxxx进行了绑定`WebMvcProperties.class`  `WebProperties.class`

  + ```java
    //WebMvcProperties.class==spring.mvc
    //ResourceProperties.class==spring.resources
    //WebProperties.class==spring.web
    ```











##### 1.配置类只有一个有参构造器

```java
//有参构造器所有参数的值都会从容器中找
//ResourceProperties resourceProperties：获取和spring.resources绑定的所有的值的对象
//WebMvcProperties mvcProperties：获取和spring.mvc绑定的所有的值的对象
//ListableBeanFactory beanFactory  Spring的beanFactory
//HttpMessageConverters：找到所有的HttpMessageConverter
//ResourceHandlerRegistrationCustomizer 找到 资源处理器的自定义器。=========
//DispatcherServletPath  
//ServletRegistrationBean   给应用注册Servlet、Filter....
public WebMvcAutoConfigurationAdapter(
      org.springframework.boot.autoconfigure.web.ResourceProperties resourceProperties,
      WebProperties webProperties, WebMvcProperties mvcProperties, ListableBeanFactory beanFactory,
      ObjectProvider<HttpMessageConverters> messageConvertersProvider,
      ObjectProvider<ResourceHandlerRegistrationCustomizer> resourceHandlerRegistrationCustomizerProvider,
      ObjectProvider<DispatcherServletPath> dispatcherServletPath,
      ObjectProvider<ServletRegistrationBean<?>> servletRegistrations) {
   this.resourceProperties = resourceProperties.hasBeenCustomized() ? resourceProperties
         : webProperties.getResources();
   this.mvcProperties = mvcProperties;
   this.beanFactory = beanFactory;
   this.messageConvertersProvider = messageConvertersProvider;
   this.resourceHandlerRegistrationCustomizer = resourceHandlerRegistrationCustomizerProvider.getIfAvailable();
   this.dispatcherServletPath = dispatcherServletPath;
   this.servletRegistrations = servletRegistrations;
   this.mvcProperties.checkConfiguration();
}
```





##### 2.资源处理的默认规则

```java
public void addResourceHandlers(ResourceHandlerRegistry registry) {
   if (!this.resourceProperties.isAddMappings()) {
      logger.debug("Default resource handling disabled");
      return;
   }
    //webjars的规则
   addResourceHandler(registry, "/webjars/**", "classpath:/META-INF/resources/webjars/");
   addResourceHandler(registry, this.mvcProperties.getStaticPathPattern(), (registration) -> {
      registration.addResourceLocations(this.resourceProperties.getStaticLocations());
      if (this.servletContext != null) {
         ServletContextResource resource = new ServletContextResource(this.servletContext, SERVLE
         registration.addResourceLocations(resource);
      }
   });
          
private void addResourceHandler(ResourceHandlerRegistry registry, String pattern, String... locations) {
	addResourceHandler(registry, pattern, (registration) -> registration.addResourceLocations(locations));
}
          
private void addResourceHandler(ResourceHandlerRegistry registry, String pattern,
		Consumer<ResourceHandlerRegistration> customizer) {
	if (registry.hasMappingForPattern(pattern)) {
		return;
	}
	ResourceHandlerRegistration registration = registry.addResourceHandler(pattern);
	customizer.accept(registration);
	registration.setCachePeriod(getSeconds(this.resourceProperties.getCache().getPeriod()));
	registration.setCacheControl(this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl());
	registration.setUseLastModified(this.resourceProperties.getCache().isUseLastModified());
	customizeResourceHandlerRegistration(registration);
}
```



```yaml
spring:
  resources:
    add-mappings: false   禁用所有静态资源规则
```



```java
//静态资源的默认位置
public static class Resources {
	private static final String[] CLASSPATH_RESOURCE_LOCATIONS = { "classpath:/META-INF/resources/",
			"classpath:/resources/", "classpath:/static/", "classpath:/public/" };
	/**
	 * Locations of static resources. Defaults to classpath:[/META-INF/resources/,
	 * /resources/, /static/, /public/].
	 */
	private String[] staticLocations = CLASSPATH_RESOURCE_LOCATIONS;
```



##### 3.欢迎页的处理规则

```java
HandlerMapping：处理器映射，保存了每一个Handler能处理哪些请求
    
@Bean
public WelcomePageHandlerMapping welcomePageHandlerMapping(ApplicationContext applicationContext,
      FormattingConversionService mvcConversionService, ResourceUrlProvider mvcResourceUrlProvider) {
   WelcomePageHandlerMapping welcomePageHandlerMapping = new WelcomePageHandlerMapping(
         new TemplateAvailabilityProviders(applicationContext), applicationContext, getWelcomePage(),
         this.mvcProperties.getStaticPathPattern());
   welcomePageHandlerMapping.setInterceptors(getInterceptors(mvcConversionService, mvcResourceUrlProvider));
   welcomePageHandlerMapping.setCorsConfigurations(getCorsConfigurations());
   return welcomePageHandlerMapping;
}


WelcomePageHandlerMapping(TemplateAvailabilityProviders templateAvailabilityProviders,
		ApplicationContext applicationContext, Resource welcomePage, String staticPathPattern) {
	if (welcomePage != null && "/**".equals(staticPathPattern)) {
        //要用欢迎页功能，不能有前缀，必须是/**
		logger.info("Adding welcome page: " + welcomePage);
		setRootViewName("forward:index.html");
	}
	else if (welcomeTemplateExists(templateAvailabilityProviders, applicationContext)) {
        //调用Controller   /index
		logger.info("Adding welcome page template: index");
		setRootViewName("index");
	}
}
```





### 3.请求参数处理

#### 0、请求映射

#### 1、rest使用与原理



### 4.响应数据与内容协商



### 5.视图解析与模板引擎



### 6.拦截器



### 7.跨域



### 8.异常处理



### 9.原生组件注入



### 10.嵌入式Web容器



### 11.定制化原理
