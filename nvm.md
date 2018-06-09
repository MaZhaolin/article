---
title: 使用nvm管理NodeJs
date: 2018-06-09 21:11:02
tags: 
- 工具
- NodeJs
---

## 安装nvm
curl 方式
``` bash
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.1/install.sh | bash
```
wget 方式
``` bash
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.1/install.sh | bash
```
<!-- more -->
如果提示找不到nvm命令，把下面代码放到`.zshrc`最下面
``` bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
```
并执行`source ~/.zshrc`，使用`bash`同理。

## 切换淘宝镜像
把下面代码放到`.zshrc`最下面
``` bash
export NVM_NODEJS_ORG_MIRROR=http://npm.taobao.org/mirrors/node
```
并执行`source ~/.zshrc`，使用`bash`同理。

## 使用 
安装
```
nvm i stable
```
切换
```
nvm use v10
```