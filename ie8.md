---
title: uglifyjs压缩代码遇到的ie8兼容性问题
date: 2018-05-31 17:32:45
tags:
- ie8
- javascript
---



```javascript
var a = function() {
    this.show();
}
a.prototype = {
    show: function() {}
}
```

压缩后变成了 （大致结构）

```javascript
var a = function b() {
    this.show();
}
a.prototype = {
    show: function() {}
}
```

在其他版本浏览器正常运行，因为这里`b`只在函数中有效且等于`a`，函数外部输出b则为`undefined`，但是如果使用ie8，`b`则变成了一个独立函数，并且与`a`不等于`b`。所以执行`new a()`时就会报对象不支持此方法的错误，因为此时`this`会在`b`的原型链中查找方法。



但是奇怪的只有一个地方出现了这个问题，其他地方正常，尚未找到原因。