MySql驱动8.0.11版本的一些使用注意事项
1>解决java.sql.SQLException: The server time zone value '???ú±ê×??±??' is unrecognized or represents more than one time zone.

添加格式：

?serverTimezone=GMT%2B8;
使用的数据库是MySQL，驱动是8.0.11，这是由于数据库和系统时区差异所造成的，在jdbc连接的url后面加上serverTimezone=GMT即可解决问题，如果需要使用gmt+8时区，需要写成GMT%2B8，否则会被解析为空。再一个解决办法就是使用低版本的MySQL jdbc驱动，5.1.28不会存在时区的问题。

2>解决：Fri May 18 15:00:19 CST 2018 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.

在后面加入：useSSL=false

3>解决：Loading class `com.mysql.jdbc.Driver'. This is deprecated. The new driver class is `com.mysql.cj.jdbc.Driver'. The driver is automatically registered via the SPI and manual loading of the driver class is generally unnecessary.

不能这样 driverClass="com.mysql.jdbc.Driver" 写了，应该修改成 driverClass="com.mysql.cj.jdbc.Driver"





```java
jdbc:mysql://localhost:3306/jdbcstudy?serverTimezone=GMT%2B8&useSSL=false&useUnicode=true&characterEncoding=utf8
```

