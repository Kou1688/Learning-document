# 1.环境搭建

项目架构决定如何搭建开发环境

## 1.1 项目架构

项目架构分类：

+ 业务架构
  + 顶层：用户服务，如注册登录，大会员，感兴趣视频推荐
  + 中间层：在线视频流播放+试试弹幕
  + 底层：管理后台，如视频上传，数据统计，系统消息推送等等
+ 技术架构
  + 技术选型：SpringBoot2.x+mysql+mybatis-plus+maven
+ 部署架构
  + 前端：服务转发+负载均衡
  + 后端：业务处理+功能实现
  + 工具：缓存、队列

![image-20220330143551831](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220330143551831.png)





# 2.开发基本功能

## 2.1 通用功能配置

通用功能：加解密工具（AES、RSA、MD5）、统一结果返回类

配置：json转换，全局异常处理

```java
/**
 * json转换
 *
 * @author KouChaoJie
 * @since: 2022/3/29 10:47
 */
@Configuration
public class JsonConvertorConfig {
    @Bean
    @Primary
    public HttpMessageConverters httpMessageConverters() {
        FastJsonHttpMessageConverter fastJsonHttpMessageConverter = new FastJsonHttpMessageConverter();
        FastJsonConfig fastJsonConfig = new FastJsonConfig();
        //设置时间格式
        fastJsonConfig.setDateFormat("yyyy-MM-dd HH:mm:ss");
        fastJsonConfig.setSerializerFeatures(
                //格式化json
                SerializerFeature.PrettyFormat,
                //空值转换
                SerializerFeature.WriteMapNullValue,
                SerializerFeature.WriteNullListAsEmpty,
                SerializerFeature.WriteNullStringAsEmpty,
                //排序
                SerializerFeature.MapSortField,
                //关闭循环引用
                SerializerFeature.DisableCircularReferenceDetect
        );
        fastJsonHttpMessageConverter.setFastJsonConfig(fastJsonConfig);
        return new HttpMessageConverters(fastJsonHttpMessageConverter);
    }
}
```

![image-20220329132733287](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220329132733287.png)





### 2.1.1 错误码配置

+ 用户服务：10





## 2.2 用户注册与登录

数据库表设计：

+ 用户表
+ 用户信息表

相关接口：

+ 获取RSA公钥
+ 用户注册与登录
+ 登录基于JWT进行用户的token验证



### 2.2.1 基于JWT的用户token验证

+ 验证过程

  服务端验证浏览器携带的用户名和密码，验证通过后生成用户令牌（token）并返回给浏览器，浏览器再次访问时携带token，服务端校验token并返回相关数据。

+ token不储存在服务器，不会造成服务器压力；token可以存储在非cookie中，安全性高；分布式系统下扩展性强。

  JWT分成三部分，第一部分是头部（header），第二部分是载荷（payload），第三部分是签名（signature）

  ![image-20220331165745629](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220331165745629.png)
