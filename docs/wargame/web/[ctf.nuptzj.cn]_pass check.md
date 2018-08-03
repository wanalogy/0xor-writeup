### pass check

## 题目

> http://ctf.nuptzj.cn/challenges#pass%20check
>
> ```php
> <?php
> $pass=@$_POST['pass'];
> $pass1=***********;//被隐藏起来的密码
> if(isset($pass))
> {
> if(@!strcmp($pass,$pass1)){
> echo "flag:nctf{*}";
> }else{
> echo "the pass is wrong!";
> }
> }else{
> echo "please input pass!";
> }
> ?>
> ```

## 解答思路

- > int strcmp ( string  $str1 , string \$str2 )// 参数 str1第一个字符串。str2第二个字符串。如果 str1 小于 str2 返回 < 0； 如果 str1 大于 str2 返回 > 0；如果两者相等，返回 0。 

- > 可知，传入的期望类型是字符串类型的数据，但是如果我们传入非字符串类型的数据的时候，这个函数将会有怎么样的行为呢？实际上，当这个函数接受到了不符合的类型，这个函数将发生错误，但是在5.3之前的php中，显示了报错的警告信息后，将return 0 !!!! 也就是虽然报了错，但却判定其相等了。这对于使用这个函数来做选择语句中的判断的代码来说简直是一个致命的漏洞，当然，php官方在后面的版本中修复了这个漏洞，使得报错的时候函数不返回任何值。strcmp只会处理字符串参数，如果给个数组的话呢，就会返回NULL,而判断使用的是==，NULL==0是 bool(true) 



## 解题步骤

1. hackbar提交post数据：

   ```http
   pass[]=a
   ```
   得到flag

## 漏洞挖掘思路

- todo

## 自动化解析

- TODO

- 判断题目特征：`web` `php` `md5 collision`
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串

