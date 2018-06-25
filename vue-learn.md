### 创建一个只读属性

```javascript
var a = Object.create()
Object.defineProperty(a, 'c', {
  get: function() {return 1},
  set: function(v) {console.log('c is read only')}
})
console.log(a.c) // 1
a.c = 3 // c is read only
```

### 使用`Performance`监测性能

```javascript
Performance.mark('load-start') //标记
Performance.end('load-end')
Performance.mesurce('load', 'load-start', 'load-end') // 记录两标记之间的数据
Performance.getEnteriesByName('load')[0] //获取
// 得到PerformanceEntry 对象， 属性如下
PerformanceEntry = {
  duration
  entryType
  name
  startTime
}
```

### 技巧

```javascript
//赋值
var a = b && c;
```

