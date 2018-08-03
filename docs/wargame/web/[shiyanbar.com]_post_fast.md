# 天下武功唯快不破

## 题目

> 看看响应头
>
> 格式：CTF{ }

## 流程图

## 解题步骤

#### 参考文章

#### 步骤

##### 0x1 查看响应

1. 响应头

```http
Connection: Keep-Alive
Content-Length: 216
Content-Type: text/html
Date: Fri, 20 Jul 2018 11:13:13 GMT
FLAG: UDBTVF9USElTX1QwX0NINE5HRV9GTDRHOnhtNW5JdEMzbg==
Keep-Alive: timeout=5, max=100
Server: Apache/2.4.18 (Win32) OpenSSL/1.0.2e PHP/5.3.29
```

​	发现了flag

2. 响应体

   ```html
   
   There is no martial art is indefectible, while the fastest speed is the only way for long success.</br>>>>>>>----You must do it as fast as you can!----<<<<<<</br>
   <!-- please post what you find with parameter:key -->
   ```

   发现提到，使用参数key来提交

##### 0x2 pytho提交

```python
import urllib2
import urllib
import base64
url = r'http://ctf5.shiyanbar.com/web/10/10.php'


response = urllib2.urlopen(url)
flag = response.info().getheader('FLAG')
flag = base64.b64decode(flag)
flag = flag.split(':')[1]

postdata = urllib.urlencode({'key':flag})
request = urllib2.Request(url,data = postdata)
response = urllib2.urlopen(request)
print response.read()
```

## 解答思路

1. 全面查看请求得到的信息
2. 使用python按照要求提交


## 漏洞挖掘思路

- 无

## 自动化解析

- TODO

- 判断题目特征：`web` `php` `md5` 
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串

