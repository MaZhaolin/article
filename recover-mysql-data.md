---
title: 误删mysql数据之后 ，恢复数据的的方法
date:  2016-11-13 14:26:21
tags: mysql
---

利用mysql的二进制日志记录文件来恢复数据

就在昨天我在修改线上数据库字段的时候 ，改了一个不合适的字段长度，导致数据丢失，我立马关闭了网站，开始恢复数据。期初很慌张，以前没遇到过这种问题 。后来慢慢爬文，知道可以利用数据库二进制日志文件来恢复数据。于是我利用PHP来解析二进制文件找回了数据。

<!-- more -->

### 开启二进制日志记录文件

所幸数据库默认开启了二进制日志记录文件，不然我的数据全没了，损失很大，如果没开启可以用以下方式开启：

编辑my.cnf，添加:

```
log-bin=/var/log/mysql/mysql-bin.log
```

开启日志后需要`myssqladmin flush logs`才能生效。

需要注意的是log-bin指定扩展名是无效的，当mysql创建二进制日志文件时，首先创建一个以“mysql_log_bin”为名称，以“.index”为后缀的文件；再创建一个以“mysql_log_bin”为名称，以 ‘.000001’为后缀的文件。当mysql服务重新启动一次以“.000001”为后缀的文件会增加一个，并且后缀名加1递增；如果日志长度超过了 max_binlog_size的上限（默认是1G）也会创建一个新的日志文件；使用`flush logs`（mysql命令符）或者执行`mysqladmin –u –p flush-logs`（windows命令提示符）也会创建一个新的日志文件。

### 解析二进制文件中的sql语句

首先找到二进制文件，打开后会发现里面有很多SQL语句，但是很乱，有插入语句，跟新语句，还有一些其他日志记录的标志，在这里我只需要插入到order表中的数据 ，所以以此为例，编写php来解析我需要的sql插入语句。

```php
function getSql(){
  $s = preg_quote(“INSERT INTO order (price,type,uid,info,subtime) VALUES (“);
  $str= file_get_contents('/usr/data/mysql-bin.000013');
  preg_match_all('/\{.*?\}/',$str,$arr);
  return $arr[0];
}
```

执行这个函数返回一个数组，这个数组里面就是二进制文件中所有向order表中插入数据的SQL语句。然后在循环执行数组中的SQL语句至此所有数据就恢复了。