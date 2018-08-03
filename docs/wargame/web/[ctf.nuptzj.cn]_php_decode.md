## 题目

> ### php decode
>
> ```php
> <?php
> function CLsI($ZzvSWE) {
> 
>     $ZzvSWE = gzinflate(base64_decode($ZzvSWE));
> 
>     for ($i = 0; $i < strlen($ZzvSWE); $i++) {
> 
>         $ZzvSWE[$i] = chr(ord($ZzvSWE[$i]) - 1);
> 
>     }
> 
>     return $ZzvSWE;
> 
> }eval(CLsI("+7DnQGFmYVZ+eoGmlg0fd3puUoZ1fkppek1GdVZhQnJSSZq5aUImGNQBAA=="));?>
> ```

## 解答思路

- 对于php代码，可以尝试运行给出的代码，并输出各个变量的值


## 解题步骤

1. 修改代码，在for循环下面添加如下代码：

   ```php
   echo $ZzvSWE;
   ```

2. 在php[在线代码调试网站](http://www.dooccn.com/php5.6/)中运行该程序，得到结果

   > flag:nctf{gzip_base64_hhhhhh}

## 漏洞挖掘思路

- 无

## 自动化解析

- TODO

- 判断题目特征：`web` `php` `md5 collision`
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串

