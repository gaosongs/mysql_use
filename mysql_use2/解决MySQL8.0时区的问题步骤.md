# 解决MySQL8.0时区的问题步骤

这篇文章主要介绍了解决MySQL8.0时区的问题步骤，文中通过示例代码介绍的非常详细，对大家的学习或者工作具有一定的参考学习价值，需要的朋友们下面随着小编来一起学习学习吧



### 软件版本

- Windows：Windows10
- MySQL：mysql-8.0.16-winx64.zip

### 问题描述 

java将当前时间保存到MySQL数据库时，MySQL中的时间不正确

### 问题分析 

原因一：java数据库连接使用UTC时区（世界标准时间），即serverTimezone=UTC

```
url:jdbc:mysql://localhost:3306/test?serverTimezone=UTC&useUnicode=true&characterEncoding=utf8&useSSL=true
```

原因二：MySQL使用的time_zone属性是+00:00，而北京时间比UTC时间早8小时，即UTC+08:00

### 检查MySQL 

```sql
/**
 * 可查看全局的时区设置和会话的时区设置
 * global.time_zone负责java连接数据库时的时区设置
 * session.time_zone负责Navicat客户端连接数据库时的时区设置
 * /
mysql> select @@global.time_zone,@@session.time_zone;

/**
 * 还有一种方法
 * System代表采用系统时区
 * CST是一种很乱的时区，它包括了4个时区
 * /
mysql> show variables like '%time_zone%'; 

// 验证时区，这行命令可以检测session.time_zone是否正确配置
mysql> select now();


```

### 解决方法 

步骤一：修改java中的时区为东八区

```sql
// serverTimezone可以设置为北京时间GMT%2B8、上海时间Asia/Shanghai或者香港时间Hongkong
url: jdbc:mysql://localhost:3306/test?serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=utf8&useSSL=true

```

步骤二：修改MySQL数据库的时区为东八区

```sql
// 方法一：使用命令（优点：不需要重启MySQL服务，缺点：一旦MySQL服务被重启，设置就会消失）
mysql> set time_zone = '+8:00';
mysql> set global time_zone = '+8:00';
// 方法二：修改my.ini配置文件（优点：永久保存设置，缺点：需重启MySQL服务）
[mysqld]
// 设置默认时区
default-time_zone='+8:00'
```