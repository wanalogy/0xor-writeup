# URL跳转

## 题目

> http://chinalover.sinaapp.com/web9/
>

## 解答思路

- 存在网址重定向（``302  Location``），考虑flag隐藏在重定向网页中


## 解题步骤

1. 使用bp拦截：点击超链接，访问[地址](http://chinalover.sinaapp.com/web8/search_key.php)：

   > http://chinalover.sinaapp.com/web9/

2. 在bp history中查看，得到结果

   ```http
   HTTP/1.1 302 Found
   Server: nginx
   Date: Wed, 11 Jul 2018 08:16:36 GMT
   Content-Type: text/html
   Content-Length: 0
   Connection: keep-alive
   flag: nctf{this_is_302_redirect}
   Location: http://chinalover.sinaapp.com/web8/no_key_is_here_forever.php
   Via: 1565
   ```

   

## 漏洞挖掘思路

- 无

## 自动化解析

- TODO

- 判断题目特征：`web` `php` `md5 collision`
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串

