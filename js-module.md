---
title: JavaScript模块化编程
date: 2016-12-03 18:02:19
tags: 学习笔记
---

### 立即执行函数写发

不暴露私有变量

```javascript
var module1=(function(){
  var _count=0;

  var m1=function(){
    //todo
  }

  var m2=function(){
    //todo
  }

  return {
    m1:m2,
    m2:m2
  }
})();
```



### 放大模式

继承另一个模块

```javascript
var module1=(function(mod){
  mod.m3=function(){
    //..todo
  }
  return mod;
})(module1)
```



为`module1`模块增加了一个新方法`m3()`，再放回新的`module1`模块

### 宽放大模式

在浏览器环境中，模块的各个部分通常都是从网上获取的，有时无法知道哪个部分会先加载。如果采用上一节的写法，第一个执行的部分有可能加载一个不存在空对象，这时就要采用”宽放大模式”。

```javascript
var module1=(function(mod){
  //..todo

  return mod;
})(window.module1||{});
```



### 输入全局变量

独立性是模块的重要特点，模块内部最好不与程序的其他部分直接交互。
为了在模块内部调用全局变量，必须显式地将其他变量输入模块。

```javascript
var module1=(function($,yui){
  //..todo
})(jQuery,YAHOO);
```