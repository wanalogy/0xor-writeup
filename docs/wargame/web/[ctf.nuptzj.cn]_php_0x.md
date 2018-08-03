# 起名字真难

## 题目

> ### /x00
>
> http://ctf.nuptzj.cn/challenges#%E8%B5%B7%E5%90%8D%E5%AD%97%E7%9C%9F%E9%9A%BE
>
> ```php
> <?php
>  function noother_says_correct($number)
> {
>         $one = ord('1');
>         $nine = ord('9');
>         for ($i = 0; $i < strlen($number); $i++)
>         {   
>                 $digit = ord($number{$i});
>                 if ( ($digit >= $one) && ($digit <= $nine) )
>                 {
>                         return false;
>                 }
>         }
>            return $number == '54975581388';
> }
> $flag='*******';
> if(noother_says_correct($_GET['key']))
>     echo $flag;
> else 
>     echo 'access denied';
> ?>
> ```

## 解答思路

- > 十六进制与字符串进行比较运算时，例子如下：
  >
  > ```php
  > "0x1e240"=="123456"		//true
  > "0x1e240"==123456		//true
  > "0x1e240"=="1e240"		//false
  > ```
  >
  > 当其中的一个字符串是0x开头的时候，PHP会将此字符串解析成为十进制然后再进行比较，0x1240解析成为十进制就是123456，所以与int类型和string类型的123456比较都是相等 


## 解题步骤

1. url后面添加`?key=0xccccccccc`，提交，得到flag

## 漏洞挖掘思路

- todo

## 自动化解析

- TODO

- 判断题目特征：`web` `php` `md5 collision`
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串

