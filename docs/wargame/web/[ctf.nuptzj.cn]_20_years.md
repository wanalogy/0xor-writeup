# 单身20年

## 题目

> http://chinalover.sinaapp.com/web8/
>

## 解答思路
Running the contributed command:'markdown.preview.toggleLock' failed.
- 存在网址重定向（``window.location``），考虑flag隐藏在重定向网页中


## 解题步骤

1. 使用bp拦截：点击超链接，访问[地址](http://chinalover.sinaapp.com/web8/search_key.php)：

   > http://chinalover.sinaapp.com/web8/web8/search_key.php

2. 在bp history中查看，得到结果

   ```http
   HTTP/1.1 200 OK
   Server: nginx
   Date: Wed, 11 Jul 2018 06:54:53 GMT
   Content-Type: text/html
   Connection: keep-alive
   Via: 1524
   Content-Length: 100
   
   <script>window.location="./no_key_is_here_forever.php"; </script>
   key is : nctf{yougotit_script_now}
   ```

   

## 漏洞挖掘思路

- 无

## 自动化解析

- TODO

- 判断题目特征：`web` `php` `md5 collision`
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串

