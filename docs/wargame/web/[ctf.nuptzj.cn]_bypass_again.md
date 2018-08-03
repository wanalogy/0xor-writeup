# bypass again

## 题目

> http://ctf.nuptzj.cn/challenges#bypass%20again
>
> view-source:
>
> ```php
> if (isset($_GET['a']) and isset($_GET['b'])) {
> if ($_GET['a'] != $_GET['b'])
> if (md5($_GET['a']) == md5($_GET['b']))
> die('Flag: '.$flag);
> else
> print 'Wrong.';
> }
> ```

## 解答思路

- 数组绕过：

  如果传入md5函数的参数为数组类型，则返回null，null=\==null，因此可以通过数组可以绕过 === md5判断 

- md5碰撞绕过


## 解题步骤

1. 数组绕过

   提交`?a[]=1&b[]=2`,得到答案：

   > Flag: nctf{php_is_so_cool} 

2. md5碰撞

   提交`?a=QNKCDZO&b=240610708 `，得到答案

## 漏洞挖掘思路

- todo

## 自动化解析

- TODO

- 判断题目特征：`web` `php` `md5 collision`
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串

