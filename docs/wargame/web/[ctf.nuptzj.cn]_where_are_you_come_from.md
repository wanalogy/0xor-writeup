# 你从哪里来

## 题目

> 你从哪里来
>
> http://chinalover.sinaapp.com/web22/

## 解答思路

- 通过从哪里来，得到解题思路


## 解题步骤

1. 使用bp拦截：点击超链接，访问[地址](http://chinalover.sinaapp.com/web8/search_key.php)：

   > http://chinalover.sinaapp.com/web22/

2. 修改referer为

   ```http
   Referer: http://www.google.com
   ```

3. 本地思路正确，但估计答题系统的问题，未得到正确答案

## 漏洞挖掘思路

- 无

## 自动化解析

- TODO

- 判断题目特征：`web` `php` `md5 collision`
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串

