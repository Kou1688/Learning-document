# 1.基础

## 1.1 spring基础模块

![image-20220614102426061](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220614102426061.png)

![image-20220614155351683](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220614155351683.png)

![image-20220621140458436](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220621140458436.png)

# 2.搭建框架系统架子

## 2.1 项目业务梳理系统框架搭建

**通过一个假想的项目，开发只关注模块间交互以及类的管理，核心目标：抽象出通用框架**

+ **项目业务梳理**
  
  + 前端展示系统首页的需求
  + ![image-20220622204712579](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220622204712579.png)
    + 访问首页的时候，展示头条列表和店铺类别列表
    + 管理员通过后台对头条和店铺类别进行增删改操作
+ **用例分析**
  + 获取头条列表
  + 获取店铺类别列表
  + 增加头条信息
  + 增加店铺类别信息
  + 修改头条信息
  + 修改店铺类别信息
  + 删除头条
  + 删除店铺类别

+ **数据库设计**

  + ```sql
    -- 创建tb_head_line表
    DROP TABLE IF EXISTS tb_head_line;
    create table `tb_head_line`
    (
        `line_id`        int(100)      not null auto_increment comment '头条id',
        `line_name`      varchar(100) default null comment '头条名称',
        `line_link`      varchar(200)  not null comment '头条链接',
        `line_img`       varchar(2000) not null comment '头条图片地址',
        `priority`       int(2)       default null comment '展示的优先级',
        `enable_status`  tinyint      default 0 comment '展示的优先级',
        `create_time`    datetime     default now() comment '创建时间',
        `last_edit_time` datetime     default now() comment '修改时间',
        primary key (`line_id`)
    ) engine = InnoDB
      auto_increment = 1
      default charset = utf8;
    
    -- 创建tb_shop_category表
    drop table if exists tb_shop_category;
    create table `tb_shop_category`
    (
        `shop_category_id`   int(11)      not null auto_increment comment '店铺类别id',
        `shop_category_name` varchar(100) not null default 'v' comment '店铺类别名称',
        `shop_category_desc` varchar(1000)         default '' comment '店铺类别描述',
        `shop_category_img`  varchar(2000)         default null comment '店铺类别图片地址',
        `priority`           int(2)       not null default 0 comment '店铺类别展示优先级',
        `create_time`        datetime              default null comment '创建时间',
        `last_edit_time`     datetime              default null comment '最后一次修改时间',
        `parent_id`          int(11)               default null comment '店铺类别的父类别',
        primary key (`shop_category_id`),
        key `fk_shop_category_self` (`parent_id`),
        constraint `fk_shop_category_self` foreign key (`parent_id`)
            references `tb_shop_category` (`shop_category_id`)
    ) engine = InnoDB
      auto_increment = 1
      default charset = utf8;
    ```

+ **实体类设计**



+ **架构设计**

  ![image-20220622205428760](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220622205428760.png)

  从整体到局部逐步实现，采用MVC架构

  ![image-20220621153939827](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220621153939827.png)

+ **service层框架搭建**

  ![image-20220622155737621](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220622155737621.png)

+ **controller层框架搭建**

  减少Servlet的数量

  + 如果一个servlet对应一个实体类，不符合职责单一原则，前端发送两次请求给不同的servlet处理。或将两个servlet合并成一个。
  + 一个servlet对应一个页面，若一个页面出现两个get相同请求，则doGet指代不清。

  ==参照SpringMVC，**仅通过DispatcherServlet进行请求派发**。==

  + 拦截所有请求

    `"/"`并不会拦截jsp请求

  + 解析请求

    **改写service方法，解析相关请求并进行处理**

    获取请求的路径以及请求方法，选择合适的controller进行处理

    ```java
    /**
     * 拦截所有请求,解析请求进行具体的请求派发
     *
     * @author KouChaoJie
     * @since: 2022/6/22 16:28
     */
    @WebServlet("/")
    @Slf4j
    public class DispatcherServlet extends HttpServlet {
        @Override
        protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
            log.info("当前请求路径:{}", req.getServletPath());
            log.info("当前请求方法:{}", req.getMethod());
            if ("/Main".equals(req.getServletPath()) && req.getMethod().equals(RequestMethodConstant.GET)) {
                new MainPageController().getMainPageInfo(req, resp);
            }
        }
    }
    ```
  
  + 派发给对应Controller方法进行处理



# 3.IOC功能实现前奏

每次增加controller，难道每次都要修改DispatcherServlet里的代码吗？使用设计模式来解决DispatcherServlet的开闭原则。

## 3.1 引入简单工厂模式

适用场景：

+ 需要创建的对象较少。
+ 客户端不关心对象的创建过程。

优点：

+ 可以对创建对象进行加工，对客户端隐藏细节

缺点：

+ 因创建逻辑复杂或创建对象过多会造成代码臃肿
+ 且新增，删除子类会违反开闭原则

所有设计模式原则并非一定要严格遵守，需要结合业务的实际情况。

## 3.2 引入工厂方法模式

定义一个用于创建对象的接口，让子类决定实例化哪一个类。

优点：

+ 对类的实例化延迟到其子类。
+ 遵循开闭原则。
+ 对客户端隐藏对象的创建细节。
+ 遵循单一职责。

缺点：

+ 添加子类的时候，改动成本较大。
+ 只支持同一类产品的创建。

## 3.3 引入抽象工厂模式

提供一个创建一系列相关或相互依赖对象的接口。

+ 工厂方法模式更加侧重与同一产品等级。
+ 抽象工厂模式侧重的是同一产品族 。
+ 解决了工厂模式只支持生产一种产品的弊端。
+ 并且新增一个产品族，只需要增加一个新的具体工厂，不需要修改代码。
+ 每个抽象产品派生多个具体产品类，每个抽象工厂派生多个具体工厂类，每个具体工厂负责一系列具体产品的示例创建。

缺点：

+ 添加新产品时依旧违背开闭原则，增加系统复杂度。

工厂模式并没有解决开闭原则的问题，**可以参考Spring的解决方式，结合工厂模式与反射机制实现IOC容器。**

## 3.4 反射

反射：允许程序在运行时来进行自我检查并对内部的成员进行操作。

作用：

+ 在运行时判断任意一个对象所属的类。
+ 在运行时获取类的对象。
+ 在运行时访问java对象的属性，方法，构造方法等。

在运行期间，一个类，只有一个与之相对应的Class对象产生。

![image-20220627205231456](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220627205231456.png)

## 3.5 注解

用注解来保存类相关信息以供反射调用。

注解是一种提供为程序元素设置元数据的方法，元数据即对数据的描述，数据的数据。

使用自定义注解方式来编写满足我们需求的注解。

注解的功能

+ 作为特定的标记，用于告诉编译器信息。
+ 编译时动态处理，动态生成代码。
+ 运行时动态处理，作为额外信息载体。



## 3.6 IOC前传

+ 使用注解标记需要工厂管理的实例，并根据注解属性做精细控制。做到项目中添加新的controller，service，dao时遵循开闭原则，让用户调用某个服务时不需要实例化服务所在类，做到控制反转（IOC）。
+ 使用依赖注入DI实现控制反转，把底层类作为参数传递给上层类，实现上层对下层的控制。
+ IOC容器的优势：反射+工厂模式的合体，满足开闭原则。

