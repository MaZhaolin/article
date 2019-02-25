---
title: 撸一个Vue手势验证码组件
date: 2018-08-07 11:44:53
tags: vue
---

### 手势验证码VAPTCHA

这是一种绘制轨迹进行人机的验证码，官网称为手势验证码。以下为官网说明：

VAPTCHA是“Variation Analysis based Public Turing Test to Tell Computers and Humans Apart”（基于变量分析来区分人类和计算机的图灵测试程序）的缩写，又称为手势验证码，一种基于人工智能和大数据的人机验证解决方案 。相对于传统验证码，VAPTCHA更加简单和安全。

<!--more-->

### 使用方式

[使用文档](https://www.vaptcha.com/document/install)中介绍了3种模式，点击式、嵌入式和隐藏式。其中隐藏式需要vip才能使用，并且使用方式与其他两种不同，这里就只针对点击式、嵌入式编写组件，两种方式的配置大致相同。

使用验证码之前我们需要先在官网注册并创建一个验证单元，创建之后可以获取到一个`vid`和`key`，这个`vid`就是用于前端创建验证码的重要参数。在创建验证单元的时候要填域名，这里需要注意的是，这里可以填ip，所填写的域名需要与你的使用验证码的地址一致。

这里我在codepen上写了一个实例，因为结果域名是`codepen.io `所以这里创建验证单元时域名就填这个了。

### 开始动手

创建组件`Vaptcha.vue`，文档中提供了一个预加载动画，这里直接复制过来用。

初始化验证码需要先加载`https://cdn.vaptcha.com/v2.js`，但只需要加载一次就行了，所以写一个加载脚本的函数`loadV2Script`，返回一个`Promise对象`。

```javascript
loadV2Script() {
  if (typeof window.vaptcha === 'function') { //如果已经加载就直接放回
    return Promise.resolve()
  } else {
    return new Promise(resolve => {
      var script = document.createElement('script')
      script.src = 'https://cdn.vaptcha.com/v2.js'
      script.async = true
      script.onload = script.onreadystatechange = function () {
        if (!this.readyState || this.readyState == 'loaded' || this.readyState == 'complete') {
          resolve()
          script.onload = script.onreadystatechange = null
        }
      }
      document.getElementsByTagName("head")[0].appendChild(script)
    })
  }
}
```



因为加载验证码需要先获取容器，所以我们在`mounted`里面来初始化验证按钮，这里默认会加载点击式。

```javascript
mounted() {
  this.loadV2Script().then(() => {
    window.vaptcha({
    vid: '5b68f7bffc650e121c835ffc',
    container: this.$refs.vaptcha
  }).then(obj => {
      this.$emit('input', obj)
      obj.render() //渲染按钮 
    })
  })
},
```

到这里就已经完成了组件的编写，然后就是一些优化，使用`props`为组件添加参数，完整代码如下。

具体的使用实例可以参考我在codepen上面的示例，地址:https://codepen.io/insertsweat/pen/WKgMLJ

```html
<template>
    <div ref="vaptcha" style="width: 300pxheight: 36px">
      <div class="vaptcha-init-main">
        <div class="vaptcha-init-loading">
          <a href="https: //www.vaptcha.com/" target="_blank"><img src="https: //cdn.vaptcha.com/vaptcha-loading.gif"/></a>
          <span class="vaptcha-text">VAPTCHA启动中...</span>
        </div>
      </div>
    </div>
</template>

<script>
const extend = function(to, _from) {
  for (const key in _from) {
    to[key] = _from[key]
  }
  return to
}

export default {
  props: {
    type: {
      type: String,
      default: 'click'
    },
    scene: {
      type: String,
      default: ''
    },
    vpStyle: {
      type: String,
      default: 'dark'
    },
    color: {
      type: String,
      color: '#3C8AFF'
    },
    lang: {
      type: String,
      default: 'zh-CN'
    },
  },
  mounted() {
    var config = extend({
      vid: '5b68f7bffc650e121c835ffc',
      container: this.$refs.vaptcha,
      style: this.vpStyle
    }, this.$props)
    this.loadV2Script().then(() => {
      window.vaptcha().then(obj => {
        this.$emit('input', obj)
        obj.render()
      })
    })
  }, 
  methods: {
    loadV2Script() {
      if (typeof window.vaptcha === 'function') { //如果已经加载就直接放回
        return Promise.resolve()
      } else {
        return new Promise(resolve => {
          var script = document.createElement('script')
            script.src = 'https://cdn.vaptcha.com/v2.js'
            script.async = true
            script.onload = script.onreadystatechange = function() {
              if (!this.readyState || this.readyState == 'loaded' || this.readyState == 'complete') {
                resolve()
                script.onload = script.onreadystatechange = null
              }
            }
            document.getElementsByTagName("head")[0].appendChild(script)
        })
      }
    }
  }
}
</script>

<style>
.vaptcha-init-main {
  display: table;
  width: 100%;
  height: 100%;
  background-color: #EEEEEE;
}

.vaptcha-init-loading {
  display: table-cell;
  vertical-align: middle;
  text-align: center
}

.vaptcha-init-loading>a {
  display: inline-block;
  width: 18px;
  height: 18px;
}

.vaptcha-init-loading>a img {
  vertical-align: middle
}

.vaptcha-init-loading .vaptcha-text {
  font-family: sans-serif;
  font-size: 12px;
  color: #CCCCCC;
  vertical-align: middle
}
</style>

```



