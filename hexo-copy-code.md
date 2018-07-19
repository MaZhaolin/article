---
title: 30行代码，给hexo博客添加点击复制代码的功能
date: 2018-07-19 09:00:04
tags: hexo
---





```javascript
function copyToClipboard(str) {
  var save = function (e) {
    e.clipboardData.setData('text/plain', str);
    e.preventDefault();
  }
  document.addEventListener('copy', save);
  document.execCommand('copy');
  document.removeEventListener('copy', save);
}

$('figure.highlight').map(function (k, el) {
  var btn = $('<div class="click-copy-code">单击复制代码</div>');
  $(el).css({
    'position': 'relative'
  })
  btn.css({
    'padding': '5px 10px',
    'position': 'absolute',
    'top': '0',
    'right': '0',
    'background': '#6acaf1',
    'color': '#fff',
    'font-size': '12px',
    'cursor': 'pointer',
  })
  btn.click(function () {
    var content = $(el).find('.code')[0].innerText;
    copyToClipboard(content)
  })
  $(el).append(btn[0])
})
```

