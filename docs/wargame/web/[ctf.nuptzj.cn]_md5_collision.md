# md5 collision

## 题目

> http://ctf.nuptzj.cn/challenges#md5%20collision
>
> http://chinalover.sinaapp.com/web19/
>
> ```php
> <?php
> $md51 = md5('QNKCDZO');
> $a = @$_GET['a'];
> $md52 = @md5($a);
> if(isset($a)){
> if ($a != 'QNKCDZO' && $md51 == $md52) {
>     echo "nctf{*****************}";
> } else {
>     echo "false!!!";
> }}
> else{echo "please input a";}
> ?>
> ```

## 解答思路

- 本题考到了php的弱类型比较，当两个值使用==进行比较时，只是比较变量的值，而不会去比较变量的类型，md5('QNKCDZO')的hash值为 0e830400451993494058024219903391 ，对于 0ed+ 类型的数字，==会认为该值为0，所以只需满足md5($a)的值为0ed+类型即可满足条件，并且$a != 'QNKCDZO'，这里列出一些符合条件的值： 

  ```php
  var_dump(md5('240610708') == md5('QNKCDZO'));
  var_dump(md5('aabg7XSs') == md5('aabC9RqS'));
  var_dump(sha1('aaroZmOk') == sha1('aaK1STfY'));
  var_dump(sha1('aaO8zKZF') == sha1('aa3OFF9m'));
  var_dump('0010e2' == '1e3');
  var_dump('0x1234Ab' == '1193131');
  var_dump('0xABCdef' == ' 0xABCdef');
  ```

  


## 解题步骤

1. 输入参数`$a=240610708`得到flag 

## 漏洞挖掘思路

- 由于php比较符智能**转换**导致的问题：http://www.vuln.cn/6423
- [php弱比较等一些问题的总结](http://skysec.top/2017/07/22/PHP%E5%87%BD%E6%95%B0%E9%BB%91%E9%AD%94%E6%B3%95%E5%B0%8F%E6%80%BB%E7%BB%93/)
- [另一个总结](https://blog.csdn.net/qq_31481187/article/details/60968595)
- [php漏洞汇总](https://www.restran.net/2016/09/26/php-security-notes/)

## 自动化解析

- 判断题目特征：`web` `php` `md5 collision`
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串

