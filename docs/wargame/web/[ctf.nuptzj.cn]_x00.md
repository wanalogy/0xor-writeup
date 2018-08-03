# 宽字节注入

## 题目

> ### /x00
>
> http://ctf.nuptzj.cn/challenges#/x00
>
> view-source:
>
> ```php
> if (isset ($_GET['nctf'])) {
>     if (@ereg ("^[1-9]+$", $_GET['nctf']) === FALSE)
>         echo '必须输入数字才行';
>     else if (strpos ($_GET['nctf'], '#biubiubiu') !== FALSE)   
>         die('Flag: '.$flag);
>     else
>         echo '骚年，继续努力吧啊~';
> } 
> ```

## 解答思路

- @ereg ("^[1-9]+$", $_GET['nctf'])即要求nctf变量必须是数字，google发现ereg函数存在%00截断漏洞，当遇到%00(NULL)时，函数就截止了。 
- 输入`nctf[]=` 那为什么`ereg()`也能符合呢?因为`ereg()`在出错时返回的也是`null`,`null!==false`,所以符合要求 


## 解题步骤

- 方法1：

  使用%00截断，构造payload如nctf=1%00%23biubiubiu，这里#符号需要进行URL编码，输入后的得到flag

- 方法2：

   使用数组的形式绕过，payload为：nctf[]=123,传入之后，ereg是返回NULL的，===判断NULL和FALSE，是不相等的，所以可以进入第二个判断，strpos处理数组时，也是返回NULL，注意这里的是!==，NULL!==FALSE,条件成立，拿到flag

## 漏洞挖掘思路

- todo

## 自动化解析

- TODO

- 判断题目特征：`web` `php` `md5 collision`
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串

