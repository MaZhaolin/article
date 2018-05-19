---
title: 使用python 将js文件中的中文处理为Unicode 解决discuz编码问题
date: 2018-02-23 11:04:33
tags: 
- python
- charset
- discuz
---



# 前言

开发discuz过程中最头疼的就是编码问题了，每次都要把中文作为语言包，js脚本文件就跟头疼了，特别是要用到`webpack`打包的时候

于是就想到了写个脚本来批量处理，开始用`shell`，折腾许久还是用`python`写出来了。

<!--more-->

# 代码

unicode.py

```python

def toUnicode(filepath, filename):
    fd = open(filepath, 'r')
    s = fd.read()
    s = s.encode('unicode_escape')
    s = s.decode('utf-8')
    s = s.replace('\\\\', '\\')
    f = open('./' + filename, 'w')
    f.write(s)
    f.close()
    fd.close()
    
toUnicode('/app/static/app.js', 'app.js');
```



使用:

```bash
python3 unicode.py
```

