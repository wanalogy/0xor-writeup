# cookie

## 题目

> ### COOKIE
>
> http://chinalover.sinaapp.com/web10/index.php

## 解答思路

- 提示cookie，并且提示0==not，所以应当将0改为1


## 解题步骤

1. 使用bp拦截：点击超链接，访问[地址](http://chinalover.sinaapp.com/web8/search_key.php)：

   > http://chinalover.sinaapp.com/web10/index.php

2. 修改cookie参数`Login=1`为`Login=0`

3. 得到返回结果

   ```http
   HTTP/1.1 200 OK
   Server: nginx
   Date: Wed, 11 Jul 2018 08:29:53 GMT
   Content-Type: text/html
   Connection: keep-alive
   Via: 1528
   Content-Length: 43
   
   flag:nctf{cookie_is_different_from_session}
   ```


## 漏洞挖掘思路

- 无

## 自动化解析

- TODO

- 判断题目特征：`web` `php` `md5 collision`
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串
