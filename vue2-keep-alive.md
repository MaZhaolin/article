---
title: vue-router 中使用keep-alive缓存页面
date: 2018-01-26 14:38:47
tags:
- vue
---



# keep-alive 使用

用来缓存组件,避免多次加载相应的组件,减少性能消耗

```Html
<keep-alive>
  <component>
    <!-- 组件将被缓存 -->
  </component>
</keep-alive>
```

缓存整个站点的所有页面

```Html
<keep-alive>
  <router-view></router-view>
</keep-alive>
```

<!-- more -->

# 缓存部分页面

## 使用router. meta属性

```Html
// 这是目前用的比较多的方式
<keep-alive>
    <router-view v-if="$route.meta.keepAlive"></router-view>
</keep-alive>
<router-view v-if="!$route.meta.keepAlive"></router-view>
```

`router`设置

```javascript
  routes: [
    { path: '/', redirect: '/index',  component: Index, meta: { keepAlive: true }},
    {
      path: '/common',
      component: TestParent,
      children: [
        { path: '/test2', component: Test2, meta: { keepAlive: true } }
      ]
    }
    // 表示index和test2都使用keep-alive
```

## 使用新增属性inlcude/exclude

2.1.0后提供了`include/exclude`两个属性 可以针对性缓存相应的组件

```Html
<!-- comma-delimited string -->
<keep-alive include="a,b">
  <component :is="view"></component>
</keep-alive>
<!-- regex (use v-bind) -->
<keep-alive :include="/a|b/">
  <component :is="view"></component>
</keep-alive>

//其中a,b是组件的name
```

`注意`:这种方法都是预先知道组件的名称的

## 动态判断

```html
<keep-alive :include="includedComponents">
  <router-view></router-view>
</keep-alive>
```

`includedComponents`动态设置即可
