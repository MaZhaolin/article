---
title: php中的字符编码问题
date: 2018-02-01 15:37:35
tags: php
---



# 中文编码转换

在开发discuz插件时遇到了a服务器传回了`utf-8`编码的数据，在`gbk`编码的discuz论坛中出现了乱码问题。

解决方案就是将获取到的数据进行一次编码。

在`php`中可使用`mb_convert_encoding`函数来转换字符串编码，函数有三个参数：

- `$data`: 要转码的字符串

- `$charset`: 需要的编码格式

- `$fromCharset`: 原字符编码格式

  <!-- more -->

discuz的实例代码：

常量`CHARSET`为discuz的字符编码

```Php
function characet($data, $charset = 'utf-8', $fromCharset = CHARSET){
    if( !empty($data) ){
        if( $charset != $fromCharset){
            $data = mb_convert_encoding($data ,$charset , $fromCharset);
        }
    }
  return $data;
}
```



# gbk中文编码中`json_encode`，中文返回`null`

在`gbk`的网站中使用发现所有中文消息都返回了`null`，发现原来是`json_encode`的问题。

`json_encode`在编码的时候遇到中文会返回`null`，所以需要先将数据转为`utf-8`编码。