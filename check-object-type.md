---
title: 正确的检测对象类型 Object.prototype.toString
date: 2018-02-15 14:06:31
tags: javascript
---

# 使用`typeof`

使用`typeof`会有一个问题，就是`null`和`Array`得到的结果也是`object`，所以我们需要判断一个变量是否是对象就应该用到`Object.prototype.toString`。

<!--more-->

# 检测纯粹的`object`对象

```javascript

console.log(Object.prototype.toString.call("jerry"));//[object String]
console.log(Object.prototype.toString.call(12));//[object Number]
console.log(Object.prototype.toString.call(true));//[object Boolean]
console.log(Object.prototype.toString.call(undefined));//[object Undefined]
console.log(Object.prototype.toString.call(null));//[object Null]
console.log(Object.prototype.toString.call({name: "jerry"}));//[object Object]
console.log(Object.prototype.toString.call(function(){}));//[object Function]
console.log(Object.prototype.toString.call([]));//[object Array]
console.log(Object.prototype.toString.call(new Date));//[object Date]
console.log(Object.prototype.toString.call(/\d/));//[object RegExp]
```



示例代码如下:

```javascript
function isObject(obj) {
    return Object.prototype.toString.call(obj) === '[object Object]';
}
```

不过这种方式无法区分自定义对象类型，自定义类型需要采用`instanceOf`，如

```javascript
function Person(){};
isObject(new Person()); //结果为true
```

# 为什么不用`obj.toString()`

```javascript
console.log("jerry".toString());//jerry
console.log((1).toString());//1
console.log([1,2].toString());//1,2
console.log(new Date().toString());//Wed Dec 21 2016 20:35:48 GMT+0800 (中国标准时间)
console.log(function(){}.toString());//function (){}
console.log(null.toString());//error
```

因为`toString`使用`Object`的原型方法，而`Array`等类型作为`Object`的实例都重写了`toString`方法。