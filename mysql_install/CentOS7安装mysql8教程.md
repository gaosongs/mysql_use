## [CentOS7 安装和配置MySQL8全过程记录](https://www.cnblogs.com/apanly/p/12430589.html)

## 缘由

最近由于工作调整，很多基础工作都要准备。对我们研发来讲，代码写完了还需要部署到生产环境。那就无法避免的要部署生产服务器的环境。由于软件一直会有更新迭代。这里我们就说下MySQL8.0在CentOS7安装的一个全过程

## 配置源

由于默认源已经不再有MySQL软件信息了，所以需要自己去官方下载并安装

```
wget https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm 
yum localinstall  mysql80-community-release-el7-3.noarch.rpm
yum install mysql-community-client mysql-community-server
```

## 服务操作

```
service mysqld start #启动
service mysqld restart #重启
service mysqld stop # 停掉
```



根据上面的介绍，安装之后我们需要启动服务

## 重置root密码

启动服务之后，默认会给root账号设置一个默认密码。默认密码就在 /var/log/mysqld.log 中，如下图

![image.png](http://cdn.pic1.54php.cn/20190811/4bcf7219b7076c05e189a209ac40cb44.png?imageView/2/w/600)

```
service mysqld start #启动
service mysqld restart #重启
service mysqld stop # 停掉
```

进去之后发现输入任何密码都会提示如下问题。新的版本安全方面做得非常好。情愿麻烦点也要保证安全这种思路我是赞同的

```
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.
```



![image.png](http://cdn.pic1.54php.cn/20190811/119fa31a8f74b5009097ccadb19a3c5e.png?imageView/2/w/600)

### 重置密码命令

```
ALTER USER "root"@"localhost" IDENTIFIED BY "xxxxxxx";
 
#执行如上命令 不巧的是会遇到其他错误 ，整个人都不好了，哈哈
#ERROR 1819 (HY000): Your password does not satisfy the current policy requirements
```

遵循我们遇到问题解决问题的方针坚持不易的执行下去。根据描述可以看出来就是密码不符合安全级别（默认需要大小写字母特殊字符）。按照这种规则设置密码之后就可以执行重置密码了

大家可以到 http://www.54php.cn/game/tools/index  这里去生成复杂密码（承诺不会记录任何密码信息）

### 设置密码的验证方式

在使用数据库客户端工具连接的时候可能会出现如下错误。这个是因为8.0使用了更安全的校验模式

```
Authentication plugin 'caching_sha2_password' cannot be loaded
```

这个解决方案有2个。



方案一：

改变你使用账号的校验方式 。这种操作墙裂不建议改变root账号。保证最高权限的账号安装是非常重要的

```
ALTER USER "root"@"localhost"  IDENTIFIED WITH mysql_native_password BY "xxxxxxx";
FLUSH PRIVILEGES;
```



方案二：



新增加一个账号使用支持的验证方式，并严格限制该账号的权限

```
CREATE USER root_virtual@localhost IDENTIFIED WITH mysql_native_password  BY 'yyyy';
```



## 用户授权和回收 

```
GRANT select,insert,update ON beian_cms_db.* TO 'root_virtual'@'localhost' WITH GRANT OPTION;
```

PS: 权限包括 select,insert,update,delete,create,drop,index,alter,grant,references,reload,shutdown,process,file

## 参考文章

- [https://www.cnblogs.com/yhqvod/p/8930980.html ](https://www.cnblogs.com/yhqvod/p/8930980.html)