---
title: Ubuntu环境下MySql数据库实操笔记
date: 2020-03-28 11:32:36
tags: Linux
---

## 在Ubuntu下安装MySQL数据库
系统环境：Ubuntu 18.04.4 LTS
数据库环境：MySQL 5.7.29
```bash
sudo apt-get install mysql-server
```
安装过程中没有提示要设置初始密码，使用空密码无法登录会报错
<!--more-->
```bash
sudo cat /etc/mysql/debian.cnf
# Automatically generated for Debian scripts. DO NOT TOUCH!
[client]
host     = localhost
user     = debian-sys-maint
password = TSrgdwiw4RHkTLKP
socket   = /var/run/mysqld/mysqld.sock
[mysql_upgrade]
host     = localhost
user     = debian-sys-maint
password = TSrgdwiw4RHkTLKP
socket   = /var/run/mysqld/mysqld.sock
```
使用上面的账号密码登录，然后修改密码
```bash
>use mysql;
>update mysql.user set authentication_string=password('123456') where user='root' and Host ='localhost';
>update user set  plugin="mysql_native_password"; 
>flush privileges;
>quit;
```
重启MySQL服务，重新使用root登录
```bash
sudo service mysql restart
mysql -u root -p
```

---

## 登录数据库
```bash
mysql -u root -p
#输入密码
```
### 查看当前用户下所有的数据库
```bash
show databases;#以下是初次安装完MySQL后默认的数据库
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.00 sec)
```
### 选择数据库
```bash
use mysql#数据库名
```
### 查看当前数据库下所有的表
```bash
show tables;
+---------------------------+
| Tables_in_mysql           |
+---------------------------+
| columns_priv              |
| db                        |
| engine_cost               |
| event                     |
| func                      |
| general_log               |
| gtid_executed             |
| help_category             |
| help_keyword              |
| help_relation             |
| help_topic                |
| innodb_index_stats        |
| innodb_table_stats        |
| ndb_binlog_index          |
| plugin                    |
| proc                      |
| procs_priv                |
| proxies_priv              |
| server_cost               |
| servers                   |
| slave_master_info         |
| slave_relay_log_info      |
| slave_worker_info         |
| slow_log                  |
| tables_priv               |
| time_zone                 |
| time_zone_leap_second     |
| time_zone_name            |
| time_zone_transition      |
| time_zone_transition_type |
| user                      |#保存了所有的用户信息
+---------------------------+
31 rows in set (0.01 sec)
```
### 添加用户
```bash
>INSERT INTO user (host, user, password, select_priv, insert_priv, update_priv) 
 VALUES ('localhost', 'guest',  authentication_string('guest123'), 'Y', 'Y', 'Y');
#用户名为guest，密码为guest123，并授权用户可进行 SELECT, INSERT 和 UPDATE操作权限
FLUSH PRIVILEGES;
```
```bash
>GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP
>ON mysql.*#给数据库mysql添加
>TO 'guest'@'localhost'
>IDENTIFIED BY 'guest123';
```
### 创建数据库
```bash
#有两种方法，一种是登录MySQL后，使用下面语句创建
>create DATABASE MySecret;
#另一种方法是使用root权限，直接在shell命令行里使用mysqladmin
mysqladmin -u root -p create MySecret
```
### 删除数据库
```bash
>drop database MySecret;
```
### 创建数据表
```bash
CREATE TABLE IF NOT EXISTS `secret`(
   `id` INT UNSIGNED AUTO_INCREMENT,
   `item` VARCHAR(20) NOT NULL,
   `account` VARCHAR(100) NOT NULL,
   `password` VARCHAR(100) NOT NULL,
   `email` VARCHAR(100) NOT NULL,
   PRIMARY KEY ( `id` )
)ENGINE=InnoDB DEFAULT CHARSET=utf8;
```
### 删除数据表
```bash
drop table secret;
```
### 插入数据
```bash
>INSERT INTO secret
>(item, account, password, email)
>VALUES
>("腾讯QQ", "38xxxxxx", "xxxx", "38xxxxxx@qq.com");
```

---

## 备份还原数据库
```bash
#在命令行界面输入以下命令
mysqldump -u username -p DBname table1 table2 >backup.sql
#username 比如root
#DBname 数据库的名字
# table1 表名，为空表示备份整个数据库
#backup.sql 文件前可以加上绝对路径，默认是在用户目录下生成
```
```bash
#还原数据库
mysql -u username -p DBname < backup.sql
#DBname 还原后的数据库名
```