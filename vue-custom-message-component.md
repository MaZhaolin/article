---
title: 编写一个简单的vue消息组件
date: 2018-02-14 16:46:25
tags: 
- javascript 
- vue
---

## 目录结构

- components `组件文件夹`

  - message `消息组件文件夹`
    - index.js
    - message.vue

- main.js `入口文件`

  <!-- more -->

## 编辑消息组件Message.vue

```html
<template>
  <div class="msg" :class="[`msg-${option.type}`]">
    <div class="content">
      <span class="icon"></span>
      <span class="text">{{option.msg}}</span>
    </div>
  </div>
</template>

<script>
  export default {
    data() {
      return {
          option: {
              type: 'default', //根据type改变消息类型
              msg: ''
          }
      };
    }
  };
</script>
```

## 编写index.js

```javascript
import Vue from 'vue'
import Msg from './message.vue'

const MsgBox = Vue.extend(Msg);

let Message = (opts = {type: 'default', msg: ''}) => {
    let instance = new MsgBox({
        el: document.createElement('div')
    })
    if (typeof opts === 'string') {
        instance.option.msg = opts;
    } else if(typeof opts === 'object') {
        instance.option = opts
    } else {
        console.log('msg component params type is string or object');
    }
    document.body.appendChild(instance.$el);
    setTimeout(() => {
        instance.$el.remove();
    }, 1000)
}

export default Message
```

#### 判断参数类型:

- `string`类型则直接将值赋值给`msg`选项;
- `object`类型则直接作为选项赋值
- 其他类型，抛出异常

## 使用

### 在应用的入口文件`main.js`中引入

```javascript
import Vue from 'vue'
import App from './App'
import message from './components/message';

Vue.prototype.$msg = message;

const app = new Vue({
    el: '#app',
    template: '<App/>',
    components: {
        App
    }
}) 
```

### 在组件中使用:

```javascript
export default {
    created() {
        this.$msg('success!');
    }
}
```

