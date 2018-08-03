# 签到2

## 题目

> http://teamxlc.sinaapp.com/web1/02298884f0724c04293b4d8c0178615e/index.php
>
> ```html
> <html>
> <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
> 尚未登录或口令错误<form action="./index.php" method="post">
> 	<p>输入框：<input type="password" value="" name="text1" maxlength="10"><br>
> 	请输入口令：zhimakaimen
> 	<input type="submit" value="开门">
> </form>
> 
> </html>
> ```

## 解答思路

1. 检查可能做访问控制的参数，maxlength=10
2. 而口令zhimakaimen长度为11，所以确定该题目需要bp截获提交修改post中的数据


## 解题步骤

- 略

## 漏洞挖掘思路

- 无

## 待实现

- TODO

- 判断题目特征：`web` `php` `md5 collision`
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串

