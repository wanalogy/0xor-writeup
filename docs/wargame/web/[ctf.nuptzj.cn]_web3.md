# 层层递进

## 题目

> http://chinalover.sinaapp.com/web3/
>

## 解答思路

1. 层层递进，暗示iframe多层嵌套 

   > SO.html->S0.html->SO.htm->S0.htm-->404.html 

2. 找到404页面，在script中提取

   > ```javascript
   > <!--  
   > <script src="./js/jquery-n.7.2.min.js"></script>
   > <script src="./js/jquery-c.7.2.min.js"></script>
   > <script src="./js/jquery-t.7.2.min.js"></script>
   > <script src="./js/jquery-f.7.2.min.js"></script>
   > <script src="./js/jquery-{.7.2.min.js"></script>
   > <script src="./js/jquery-t.7.2.min.js"></script>
   > <script src="./js/jquery-h.7.2.min.js"></script>
   > <script src="./js/jquery-i.7.2.min.js"></script>
   > <script src="./js/jquery-s.7.2.min.js"></script>
   > <script src="./js/jquery-_.7.2.min.js"></script>
   > <script src="./js/jquery-i.7.2.min.js"></script>
   > <script src="./js/jquery-s.7.2.min.js"></script>
   > <script src="./js/jquery-_.7.2.min.js"></script>
   > <script src="./js/jquery-a.7.2.min.js"></script>
   > <script src="./js/jquery-_.7.2.min.js"></script>
   > <script src="./js/jquery-f.7.2.min.js"></script>
   > <script src="./js/jquery-l.7.2.min.js"></script>
   > <script src="./js/jquery-4.7.2.min.js"></script>
   > <script src="./js/jquery-g.7.2.min.js"></script>
   > <script src="./js/jquery-}.7.2.min.js"></script>
   > -->
   > ```

3. 由于题目采用了分离隐藏flag的方式，未想到如何自动检测该类型的flag


## 解题步骤

- linux命令提取flag

  ```shell
  cat web3.txt | cut -d - -f 2| cut -d . -f 1| tr -d '\n' > web3_flag.txt
  ```

   ps:web3.txt的内容为上述script

## 漏洞挖掘思路

- 无

## 自动化解析

- TODO

- 判断题目特征：`web` `php` `md5 collision`
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串

