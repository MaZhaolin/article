---
title: 设置Apache，实现一个空间多个域名
date: 2016-11-13 17:53:17
tags: Apache
---

此方法利用apache的`mod_rewrite`模块，实现修改map文件添加域名且不用重启Apache



#### 步骤如下

1. 去除httpd.conf文件中`#LoadModule rewrite_module modules/mod_rewrite.so`前面的”#”号;

2. 然后再在httpd.conf最后书写如下规则：

   ```nginx
   RewriteEngine on
   RewriteMap lowercase int:tolower
   RewriteMap vhost txt:/usr/local/etc/apache/vhost.map
   RewriteCond ${lowercase:%{SERVER_NAME}} ^(.+)$
   RewriteCond ${vhost:%1} ^(/.)$ \r\nRewriteRule ^/(.)$ %1/$1
   ```

3. 其中的 `/usr/local/etc/apache` 是你的 apache 服务器配置文件所在路径，根据实际情况更改（例如在/etc/httpd/下面）。然后，在这个所在路径的目录下创建一个文件： vhost.map，内容格式为：

   ```nginx
   #你需要添加的域名    网站路径
   blog.muzishg.com  /usr/local/apache/htdocs/blog
   ```

4. 最后重启Apache服务器，之后需要再添加域名时只需要修改vhost.map文件，无需重启Apache，所有域名只需解析一条a记录到该服务器的外网IP上

