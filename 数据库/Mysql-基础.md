# MySql驱动8.0.11版本的一些使用注意事项

1>解决java.sql.SQLException: The server time zone value '???ú±ê×??±??' is unrecognized or represents more than one time zone.

添加格式：

?serverTimezone=GMT%2B8;
使用的数据库是MySQL，驱动是8.0.11，这是由于数据库和系统时区差异所造成的，在jdbc连接的url后面加上serverTimezone=GMT即可解决问题，如果需要使用gmt+8时区，需要写成GMT%2B8，否则会被解析为空。再一个解决办法就是使用低版本的MySQL jdbc驱动，5.1.28不会存在时区的问题。

2>解决：Fri May 18 15:00:19 CST 2018 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.

在后面加入：useSSL=false

3>解决：Loading class `com.mysql.jdbc.Driver'. This is deprecated. The new driver class is `com.mysql.cj.jdbc.Driver'. The driver is automatically registered via the SPI and manual loading of the driver class is generally unnecessary.

不能这样 driverClass="com.mysql.jdbc.Driver"写了，应该修改成 driverClass="com.mysql.cj.jdbc.Driver"





```java
jdbc:mysql://localhost:3306/jdbcstudy?serverTimezone=GMT%2B8&useSSL=false&useUnicode=true&characterEncoding=utf8
```



# 1.简介与环境

**数据库 ：atguigudb**

 

## 1.卸载

在卸载之前，先停止MySQL8.0的服务。按键盘上的“Ctrl + Alt + Delete”组合键，打开“任务管理器”对话框，可以在“服务”列表找到“MySQL8.0”的服务，如果现在“正在运行”状态，可以右键单击服务，选择“停  止”选项停止MySQL8.0的服务，如图所示。



|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/wpsC98E.tmp.png) |

 



### 软件的卸载

#### 通过控制面板方式

卸载MySQL8.0的程序可以和其他桌面应用程序一样直接在“控制面板”选择“卸载程序”，并在程序列表中  找到MySQL8.0服务器程序，直接双击卸载即可，如图所示。这种方式删除，数据目录下的数据不会跟着  删除。



![img](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/wpsC98F.tmp.png) 

 

#### ***\*方式\*******\*2\*******\*：通过\*******\*360\*******\*或电脑管家等软件卸载\****

略

#### ***\*方式\*******\*3\*******\*：通过安装包提供的卸载功能卸载\****

你也可以通过安装向导程序进行MySQL8.0服务器程序的卸载。

①  再次双击下载的mysql-installer-community-8.0.26.0.msi文件，打开安装向导。安装向导会自动检测已安装的MySQL服务器程序。

② 选择要卸载的MySQL服务器程序，单击“Remove”（移除），即可进行卸载。



![img](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/wpsC990.tmp.png) 

③ 单击“Next”（下一步）按钮，确认卸载。



|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/wpsC991.tmp.png) |

 



④ 弹出是否同时移除数据目录选择窗口。如果想要同时删除MySQL服务器中的数据，则勾选“Remove the data directory”，如图所示。



![img](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/wpsC9A2.tmp.png) 

⑤ 执行卸载。单击“Execute”（执行）按钮进行卸载。



|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/wpsC9A3.tmp.png) |

 



⑥ 完成卸载。单击“Finish”（完成）按钮即可。如果想要同时卸载MySQL8.0的安装向导程序，勾选“Yes，

Uninstall MySQL Installer”即可，如图所示。



![img](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/wpsC9A4.tmp.png) 

 

### ***\*步骤\*******\*3\*******\*：残余文件的清理\****

如果再次安装不成功，可以卸载后对残余文件进行清理后再安装。

（1） 服务目录：mysql服务的安装目录

（2） 数据目录：默认在C:\ProgramData\MySQL

![img](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/wpsC9A5.tmp.png)如果自己单独指定过数据目录，就找到自己的数据目录进行删除即可。



|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/wpsC9A6.tmp.png) |

 



 

### ***\*步骤\*******\*4\*******\*：清理注册表（选做）\****

![img](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/wpsC9A7.tmp.png)如果前几步做了，再次安装还是失败，那么可以清理注册表。如何打开注册表编辑器：在系统的搜索框中输入



![img](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/wpsC9A8.tmp.png)

 

![img](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/wpsC9A9.tmp.png)

 

### ![img](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/wpsC9AA.tmp.png)***\*步骤\*******\*5\*******\*：删除环境变量配置\****

找到path环境变量，将其中关于mysql的环境变量删除，**切记不要全部删除。**

例如：删除 D:\develop_tools\mysql\MySQLServer8.0.26\bin; 这个部分



|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/wpsC9AB.tmp.png) |

 





# 2.SQL语句

## 2.1 select查询

DDL：数据定义语言

DML：数据操作语言

DCL：数据控制语言



### 2.1.1 去重 null desc操作

 基本语句不再赘述。

别名：as 空格 双引号都可以

去重：distinct

```sql
-- 去重
SELECT DISTINCT `department_id` FROM `employees`;
```

null不等同于0

null值参与运算，结果一定为null



```sql
-- 查询常数
SELECT 'CUCN', employee_id, email
FROM employees;
```

```sql
-- 显示表结构
DESCRIBE employees;
```





### 2.1.2 where

```sql
SELECT *
FROM employees
WHERE last_name = 'King';
```





## 2.2 运算符

运算符优先级：

![image-20220225103241503](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220225103241503.png)





**比较运算符：**

![image-20220225103355566](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220225103355566.png)

![image-20220225103430236](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220225103430236.png)







## 2.3 排序与分页

升序/降序

ORDER BY  ASC/DESC

ORDER BY写在where后面

```sql
-- 排序与分页
SELECT *
FROM employees
WHERE employee_id IN (101,102,103,104)
ORDER BY employee_id ASC;

-- 每页显示10条,显示第1页
SELECT * FROM employees LIMIT 0,10;
-- 公式= LIMIT (pageNum-1)*pageSize,pageSize
```

MySQL 8.0中可以使用“LIMIT 3 OFFSET 4”，意思是获取从第5条记录开始后面的3条记录，和“LIMIT 4,3;”返回的结果相同。







## 2.4 多表查询

出现笛卡尔积错误的原因：缺少连接条件









## 2.5 内连接与外连接

内连接: 合并具有同一列的两个以上的表的行, **结果集中不包含一个表与另一个表不匹配的行**

外连接: 两个表在连接过程中除了返回满足连接条件的行以外**还返回左（或右）表中不满足条件的**行 ，这种连接称为左（或右） 外连接。没有匹配的行时, 结果表中相应的列为空(NULL)。

如果是左外连接，则连接条件中左边的表也称为 **主表** ，右边的表称为 ==从表==。

如果是右外连接，则连接条件中右边的表也称为 **主表** ，左边的表称为 ==从表==。 

```sql
-- 内连接
SELECT last_name, department_name
FROM employees e
         JOIN departments d ON d.department_id = e.department_id;

-- 左外连接
SELECT last_name, department_name
FROM employees e
         LEFT JOIN departments d
             ON d.department_id = e.department_id;

-- 右外连接
SELECT last_name, department_name
FROM employees e
         RIGHT JOIN departments d
             ON d.department_id = e.department_id;
```

UNION与UNION ALL的操作

UNION：会执行去重操作

UNION ALL：不会执行去重操作

如果明确知道合并数据后的结果数据不存在重复数据，或者不需要去除重复的数据，则尽量使用UNION ALL语句，以提高数据查询的效率。

![image-20220227162659610](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220227162659610.png)



自然连接：

NATUAL  JOIN

会自动查询两张连接表中所有相同的字段，然后进行等值连接。









## 2.6 SQL执行过程

+ select语句完整结构

  ```sql
  select ...,...,...(存在聚合函数)
  from ...,JOIN.... ON
  where 过滤条件(不包含聚合函数)
  group by ...,...
  having 包含聚合函数的过滤条件
  order by ...,...(ASC/DESC)
  limit ...,...
  ```

+ sql语句执行过程

  ```sql
  from->on->join->where->group by->having->select->order by ->limit
  ```

  
