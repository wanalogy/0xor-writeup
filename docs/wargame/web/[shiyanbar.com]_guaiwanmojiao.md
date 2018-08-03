# 拐弯抹角

## 题目

> [http://ctf5.shiyanbar.com/indirection/ ](javascript:;) 
>
> 如何欺骗服务器，才能拿到Flag？
>
> 格式：CTF{}

题目类型：PHP审查

## 流程图

## 解题步骤

#### 参考文章

#### 步骤

##### 0x1 查看PHP代码

```php+HTML
Flag:
<?php 
// code by SEC@USTC 

echo '<html><head><meta http-equiv="charset" content="gbk"></head><body>'; 

$URL = $_SERVER['REQUEST_URI']; 
//echo 'URL: '.$URL.'<br/>'; 
$flag = "CTF{???}"; 

$code = str_replace($flag, 'CTF{???}', file_get_contents('./index.php')); 
$stop = 0; 

//这道题目本身也有教学的目的 
//第一，我们可以构造 /indirection/a/../ /indirection/./ 等等这一类的 
//所以，第一个要求就是不得出现 ./ 
if($flag && strpos($URL, './') !== FALSE){ 
    $flag = ""; 
    $stop = 1;        //Pass 
} 

//第二，我们可以构造 \ 来代替被过滤的 / 
//所以，第二个要求就是不得出现 ../ 
if($flag && strpos($URL, '\\') !== FALSE){ 
    $flag = ""; 
    $stop = 2;        //Pass 
} 

//第三，有的系统大小写通用，例如 indirectioN/ 
//你也可以用?和#等等的字符绕过，这需要统一解决 
//所以，第三个要求对可以用的字符做了限制，a-z / 和 . 
$matches = array(); 
preg_match('/^([0-9a-z\/.]+)$/', $URL, $matches); 
if($flag && empty($matches) || $matches[1] != $URL){ 
    $flag = ""; 
    $stop = 3;        //Pass 
} 

//第四，多个 / 也是可以的 
//所以，第四个要求是不得出现 // 
if($flag && strpos($URL, '//') !== FALSE){ 
    $flag = ""; 
    $stop = 4;        //Pass 
} 

//第五，显然加上index.php或者减去index.php都是可以的 
//所以我们下一个要求就是必须包含/index.php，并且以此结尾 
if($flag && substr($URL, -10) !== '/index.php'){ 
    $flag = ""; 
    $stop = 5;        //Not Pass 
} 

//第六，我们知道在index.php后面加.也是可以的 
//所以我们禁止p后面出现.这个符号 
if($flag && strpos($URL, 'p.') !== FALSE){ 
    $flag = ""; 
    $stop = 6;        //Not Pass 
} 

//第七，现在是最关键的时刻 
//你的$URL必须与/indirection/index.php有所不同 
if($flag && $URL == '/indirection/index.php'){ 
    $flag = ""; 
    $stop = 7;        //Not Pass 
} 
if(!$stop) $stop = 8; 

echo 'Flag: '.$flag; 
echo '<hr />'; 
for($i = 1; $i < $stop; $i++) 
    $code = str_replace('//Pass '.$i, '//Pass', $code); 
for(; $i < 8; $i++) 
    $code = str_replace('//Pass '.$i, '//Not Pass', $code); 


echo highlight_string($code, TRUE); 

echo '</body></html>';
```

​	发现了flag

2. 响应体

   ```html
   
   There is no martial art is indefectible, while the fastest speed is the only way for long success.</br>>>>>>>----You must do it as fast as you can!----<<<<<<</br>
   <!-- please post what you find with parameter:key -->
   ```

   发现提到，使用参数key来提交

##### 0x2 pytho提交

```python
import urllib2
import urllib
import base64
url = r'http://ctf5.shiyanbar.com/web/10/10.php'


response = urllib2.urlopen(url)
flag = response.info().getheader('FLAG')
flag = base64.b64decode(flag)
flag = flag.split(':')[1]

postdata = urllib.urlencode({'key':flag})
request = urllib2.Request(url,data = postdata)
response = urllib2.urlopen(request)
print response.read()
```

## 解答思路

1. 全面查看请求得到的信息
2. 使用python按照要求提交


## 漏洞挖掘思路

- 无

## 自动化解析

- TODO

- 判断题目特征：`web` `php` `md5` 
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串

