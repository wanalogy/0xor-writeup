# sql injection 4

## 题目

> http://ctf.nuptzj.cn/challenges#sql%20injection%204
>
> ```php+HTML
> <?php
> #GOAL: login as admin,then get the flag;
> error_reporting(0);
> require 'db.inc.php';
> 
> function clean($str){
> 	if(get_magic_quotes_gpc()){
> 		$str=stripslashes($str);
> 	}
> 	return htmlentities($str, ENT_QUOTES);
> }
> 
> $username = @clean((string)$_GET['username']);
> $password = @clean((string)$_GET['password']);
> 
> $query='SELECT * FROM users WHERE name=\''.$username.'\' AND pass=\''.$password.'\';';
> $result=mysql_query($query);
> if(!$result || mysql_num_rows($result) < 1){
> 	die('Invalid password!');
> }
> 
> echo $flag;
> ?>
> ```

## 解答思路

- 一般sql注入的思路是在输入中加入`’`,达到闭合前面查询条件的目的，但是由于`htmlentities($str, ENT_QUOTES)`方法的存在，会将所有的html标签转义，并且将`'`和`"`都转义掉，所以，没有办法使用添加`'`的办法来实现跳出闭合；

- 这里使用了转义字符在`htmlentities`中未被转义这一点，来实现跳出查询语句闭合，从而让mysql将自己的输入作为sql语句来执行

  > 代码中使用了htmlentities对单引号进行了转换，所以我们不能通过单引号来闭合sql语句了，根据提示，可以通过反斜杠来吃掉单引号，即对单引号进行转义 


## 解题步骤

1. url后面添加`?username=admin\&password=or 1%23`，提交，得到flag

## 漏洞挖掘思路

- todo

## 自动化解析

- TODO

- 判断题目特征：`web` `php` `md5 collision`
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串

