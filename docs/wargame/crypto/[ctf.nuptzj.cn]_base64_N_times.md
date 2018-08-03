## 题目

> http://chinalover.sinaapp.com/decode1/base64.txt

## 解题

### 思路

1. 提示多次base64加密，使用python

### 步骤

- python代码实现

  ```python
  # coding:UTF-8
  import base64
  import urllib2
  url = 'http://chinalover.sinaapp.com/decode1/base64.txt'
  data = urllib2.urlopen(url).read().replace("\n", "")
  i = 1
  while i < 20:
      data = base64.b64decode(data)
      print data
      i = i+1
  ```

  

## 待实现

- 在FeatherDuster中添加一种加密方式多次解密的方法
  - 需要判断最终解密结果的有效性，一般包含有意义的词组
  - 或者判断有不是base64加密的字符
  - 同时可以判断该字符是否符合flag的自定义格式