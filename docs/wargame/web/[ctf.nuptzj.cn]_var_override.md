# 变量覆盖

## 题目

> http://ctf.nuptzj.cn/challenges#%E5%8F%98%E9%87%8F%E8%A6%86%E7%9B%96
>
> view-source:
>
> ```php
> <?php if ($_SERVER["REQUEST_METHOD"] == "POST") { ?>
>                         <?php
>                         extract($_POST);
>                         if ($pass == $thepassword_123) { ?>
>                             <div class="alert alert-success">
>                                 <code><?php echo $theflag; ?></code>
>                             </div>
>                         <?php } ?>
>                     <?php } ?>
> ```

## 解答思路

- 参看：http://www.01happy.com/php-extract-use-and-suggestions/

- php中的extract方法可以将数组中的数据转换到当前上下文环境，其中key作为变量名，value作为变量的值。示例代码：

  ```php
  $user = array(
      'name' => 'tom'
      ,'age' => 20
  );   
   
  extract($user);
  echo $name; //输出tom
  echo $age; //输出20
  ```

  但是这样获取变量的方式比较隐秘，用一句俗话来说就是藏的很深，我个人认为不利于代码阅读。比如下面代码：

  ```php
  $name = 'anny';
  $user = array(
      'name' => 'tom'
  );   
   
  extract($user);
  echo $name; //输出tom
  ```

  $name在初始的时候被赋值为anny，但是经过extract后，$name就变成了tom，初始的赋值被覆盖了。这边代码比较简单还容易看的出来，如果是在比较复杂的代码里，有时就会纳闷了——到底哪里对$name进行重新赋值了？为什么搜索$name这个变量都搜索不到重新赋值的地方？


## 解题步骤

1. 在http://chinalover.sinaapp.com/web18/的password框中提交`d`
2. bp拦截，修改post参数为：`pass=1&thepassword_123=1`
3. 得到结果

## 漏洞挖掘思路

- todo

## 自动化解析

- TODO

- 判断题目特征：`web` `php` `md5 collision`
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串

