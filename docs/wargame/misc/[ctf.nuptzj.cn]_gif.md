## 题目

> http://chinalover.sinaapp.com/web2/index.html
>

## 解答思路

1. 二进制编辑器查看GIF
2. 在文件末尾找flag


## 解题步骤

1. 使用strings查看2.gif

   ```shell
   strings -a 2.gif|grep ctf
   ```

   得到结果：

   > ;nctf{photo_can_also_hid3_msg} 

2. 或者使用winhex查看，找到flag

## 漏洞挖掘思路

- 无

## 待实现

- TODO

- 判断题目特征：`web` `php` `md5 collision`
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串

