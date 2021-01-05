# [mysql8 安装,并且配置远程登录](https://www.cnblogs.com/jiangde/p/11835196.html)

**1.下载mysql-8.0.18-linux-glibc2.12-x86_64.tar.xz 安装包**

下载地址:https://cdn.mysql.com//Downloads/MySQL-8.0/mysql-8.0.18-linux-glibc2.12-x86_64.tar.xz

### 2.解压mysql-8.0.18-linux-glibc2.12-x86_64.tar.xz 

 

```
tar -xvf mysql-8.0.18-linux-glibc2.12-x86_64.tar.xz
```

 

### **![img](https://img2018.cnblogs.com/i-beta/1597109/201911/1597109-20191111145358919-1315782332.png)**

###  

### **3.将解压的文件重命名mysql，并移动到/usr/local目录下**

 

```
mv  mysql-8.0.18-linux-glibc2.12-x86_64 mysql
mv mysql /usr/local/mysql
```

 

![img](https://img2018.cnblogs.com/i-beta/1597109/201911/1597109-20191111150111329-1429452931.png)

### **4.进入到/usr/local目录下，创建用户和用户组并授权**

![img](https://img2018.cnblogs.com/i-beta/1597109/201911/1597109-20191111151504577-47855259.png)

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
1 cd /usr/local/
2 groupadd mysql
3 useradd -r -g mysql mysql
4 cd mysql/ #注意：进入mysql文件下授权所有的文件
5 chown -R mysql:mysql ./ 
6 
7 passwd mysql   #修改mysql用户密码
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

### **5.再/usr/local/mysql目录下，创建data文件夹**

```
 mkdir data
```

 ![img](https://img2018.cnblogs.com/i-beta/1597109/201911/1597109-20191111152056660-1832324491.png)

### **6.初始化数据库，并会自动生成随机密码，记下等下登陆要用** 

```
# bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data
```

  ![img](https://img2018.cnblogs.com/i-beta/1597109/201911/1597109-20191111152343090-1532535494.png)

### **7.修改/usr/local/mysql当前目录得用户** 

```
chown -R root:root ./
chown -R mysql:mysql data
```

![img](https://img2018.cnblogs.com/i-beta/1597109/201911/1597109-20191111152854679-1977050632.png)

### **8.# 在/etc下创建my.cnf文件,并添加文件权限**

```
touch /etc/my.cnf
chmod 777 /etc/my.cnf
```

 ![img](https://img2018.cnblogs.com/i-beta/1597109/201911/1597109-20191111153310126-32863368.png)

### **9.配置my.cnf** 

```
vim /etc/my.cnf 
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
[mysqld]
 
# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
# innodb_buffer_pool_size = 128M
 
# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
# log_bin
 
# These are commonly set, remove the # and set as required.
basedir = /usr/local/mysql
datadir = /usr/local/mysql/data
socket = /tmp/mysql.sock
log-error = /usr/local/mysql/data/error.log
pid-file = /usr/local/mysql/data/mysql.pid
tmpdir = /tmp
port = 5186
#lower_case_table_names = 1
# server_id = .....
# socket = .....
#lower_case_table_names = 1
max_allowed_packet=32M
default-authentication-plugin = mysql_native_password
#lower_case_file_system = on
#lower_case_table_names = 1
log_bin_trust_function_creators = ON
# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
# join_buffer_size = 128M
# sort_buffer_size = 2M
# read_rnd_buffer_size = 2M 
 
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

如果后期mysql运行报错，可以直接到log-error = /usr/local/mysql/data/error.log目录下直接查看错误日志

命令:cat /usr/local/mysql/data/error.log

### **10.开机自启，进入/usr/local/mysql/support-files进行设置**

```
 cd support-files/
 cp mysql.server /etc/init.d/mysql 
 chmod +x /etc/init.d/mysql
```

![img](https://img2018.cnblogs.com/i-beta/1597109/201911/1597109-20191111153932052-2092891203.png)

 

 

### **11.注册服务** 

```
 chkconfig --add mysql
```

如果命令没有，在需要处理chkconfig

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
# rpm -aq |grep chkconfig
# export PATH=/sbin:$PATH
# chkconfig
# echo $PATH
# PATH="$PATH":/sbin
# echo $PATH
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

### **12.查看是否成功**

**![img](https://img2018.cnblogs.com/i-beta/1597109/201911/1597109-20191111154255034-2094862355.png)**

### **13.etc/ld.so.conf要配置路径，不然报错** 

```
 vim /etc/ld.so.conf
 
添加如下内容:
/usr/local/mysql/lib
```

![img](https://img2018.cnblogs.com/i-beta/1597109/201911/1597109-20191111154551283-534768988.png)

### **14.配置环境变量**

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
vim /etc/profile
source /etc/profile
 
添加如下内容：
#MYSQL ENVIRONMENT
export PATH=/usr/local/mysql/bin:/usr/local/mysql/lib:$JAVA_HOME/bin:$PATH
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

![img](https://img2018.cnblogs.com/i-beta/1597109/201911/1597109-20191111154849375-183766776.png)

### **15.登陆，这里输入上面第6步随机生成得密码，细心点输入，没有显示的，登陆成功如图所示** 

![img](https://img2018.cnblogs.com/i-beta/1597109/201911/1597109-20191111155213719-220547068.png)

### **16.开启远程连接**

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
# mysql -uroot -p #进入数据库
> use mysql;#进入数据库
> select host, user, authentication_string, plugin from user;#查看用户信息
> GRANT ALL ON *.* TO 'root'@'%';#授权root用户可以远程登陆
> flush privileges;#立即生效
> ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456';#修改root用户密码
> FLUSH PRIVILEGES;#立即生效
> exit;#退出
# service mysql restart#重启mysql服务
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)