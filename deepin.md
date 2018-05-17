---
title: deepin的安装与开发环境配置
date: 2018-05-17 13:13:15
tags: linux
---



## deepin 下载安装

官网下在镜像：https://www.deepin.org，把镜像解压后里面有u盘启动盘制作工具，使用U盘一键安装。

用硬盘安装总是出现各种各样的问题，而且没有交换空间，会比较卡。

## 开发环境搭建

### 配置 oh my zsh

> 安装zsh

```bash
sudo apt-get install zsh
chsh -s /bin/zsh # 设置zsh为默认shell
```

>  安装oh my zsh

```shell
sudo apt-get install git
sudo sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

>  配置主题

```shell
# 修改 theme 为 agnoster 并保存
vim ~/.zshrc
# 安装 powerline 字体
git clone https://github.com/powerline/fonts
cd fonts 
./install.sh
```

将终端字体设置为`powerline`字体就可以看到效果了。

> 配置插件

```shell
# 安装 zsh-autosuggestion 插件
git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
```

插件配置

```js
plugins: {
  git
  z
  zsh-autosuggestion
}	
```

### NodeJs MongoDB 环境

```shell
sudo su
apt-get udpate
apt-get install nodejs
apt-get install npm 
apt-get install mongondb
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

### PHP Nginx Mysql 环境

```shell
sudo su
apt-get install nginx
apt-get install mysql
apt-get install php7.0
apt-get install php7.0-fpm
```

配置nginx

```nginx
server {
  listen 80 default_server;
  listen [::]:80 default_server;
  root /var/www/html;
  server_name _;
  
  location / {
    index index.htm index.html index.php;
    # First attempt to serve request as file, then
    # as directory, then fall back to displaying a 404.
    try_files $uri $uri/ =404;
  }
  
  location ~ \.php$ {
    root /var/www/html; #指定php的根目录
    fastcgi_pass unix:/run/php/php7.0-fpm.sock;
    fastcgi_index index.php;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    include fastcgi_params;
  }
}
```

安装php扩展示例，如使用`mysqli`则安装对应的包

```shell
sudo apt-get install php-mysql
```

