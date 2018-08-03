## 题目

> ### MYSQL
>
> http://chinalover.sinaapp.com/web11/

## 解答思路

- mysql中float等类型存在精度问题，当查询结果中存在小数位，或者使用科学计数法表示，mysql查询后会将结果四舍五入或者省略掉末位后输出，但这**不是**此考题的考点

- 本题的考点：程序的逻辑漏洞

  由于`$_GET[id]==1024`的比较中，不应该使用`$_GET[id]`，而应该使用过滤之后的`$id`，正是由于**1024.1**在过滤后为1024，即flag存在的数据项，又可以绕过判断条件，将flag显示出来

- 逻辑漏洞的查找**重点查看判断语句**


## 解题步骤

1. 访问地址：`http://chinalover.sinaapp.com/web11/robots.txt`

   > ```php
   > 别太开心，flag不在这，这个文件的用途你看完了？
   > 在CTF比赛中，这个文件往往存放着提示信息
   > 
   > TIP:sql.php
   > 
   > <?php
   > if($_GET[id]) {
   >    mysql_connect(SAE_MYSQL_HOST_M . ':' . SAE_MYSQL_PORT,SAE_MYSQL_USER,SAE_MYSQL_PASS);
   >   mysql_select_db(SAE_MYSQL_DB);
   >   $id = intval($_GET[id]);
   >   $query = @mysql_fetch_array(mysql_query("select content from ctf2 where id='$id'"));
   >   if ($_GET[id]==1024) {
   >       echo "<p>no! try again</p>";
   >   }
   >   else{
   >     echo($query[content]);
   >   }
   > }
   > ?>
   > ```
   >
   > 

2. 根据提示，访问sql.php，并提交参数`?id=1024.1`

3. 得到返回结果

   > the flag is:nctf{query_in_mysql} 

## 漏洞挖掘思路

- 无

## 自动化解析

- TODO

- 判断题目特征：`web` `php` `md5 collision`
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串

