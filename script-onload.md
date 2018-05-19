---
title: script标签的onload使用
date: 2018-02-01 14:25:38
tags: javascript
---

# onload使用小记

> ie中`onload`使用`onreadystatechange`代替。

## 作用

有时候我们需要在js加载之后执行某些业务代码，或者不同模块是用同一个js避免重复记载。

这是后就需要用到`onload`，并在执行代码时判断是否已经加载，再使用的时候需要注意单纯的判断`script`的标签是否存在是有问题的，因为标签存在不代表脚本已经记载完毕，所以需要在`onload`里给标签给一个加载成功的标记。

<!-- more -->

示例代码如下：

```javascript
var script = document.getElementById('vhelper_js');
if (script && script.loaded) {
    init() //初始化操作
} else {
    script = document.createElement('script');
    script.src = '/js/v-helper.js';
    script.id = 'vhelper_js';
  	script.onload = script.onreadystatechange = function () {
        if (!this.readyState || this.readyState === 'loaded' || this.readyState === 'complate') {
            init();
          	script.loaded = true; //加载完成标记
          	script.onload = script.onreadystatechange = null;
        }
    }
    document.getElementsByTagName("head") [0].appendChild(script);
}
```

