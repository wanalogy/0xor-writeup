## 题目

> 密文：iEJqak3pjIaZ0NzLiITLwWTqzqGAtW2oyOTq1A3pzqas 

![php](http://ctf.nuptsast.com/static/uploads/6ee943278e6d3920e04aa50d47de0c91/QQ20151023181859.png)

## 解答思路

1. 编写解密函数decode
2. 解密相应字符串

## 解题步骤

- 解密函数：

```php
<!doctype html>
<?php
function encode($str){
    $_o = strrev($str);
    for($_0=0;$_0<strlen($_o);$_0++){
        $_c = substr($_o,$_0,1);
        $__ = ord($_c)+1;
        $_c = chr($__);
        $_ = $_.$_c;
    }
    return str_rot13(strrev(base64_encode($_)));
}
function decode($str){
    $_o =  base64_decode(strrev(str_rot13($str)));
    for($_0=0;$_0<strlen($_o);$_0++){
        $_c = substr($_o,$_0,1);
        $__ = ord($_c)-1;
        $_c = chr($__);
        $_ = $_.$_c;
    }
    return strrev($_);
    
}
$str = 'iEJqak3pjIaZ0NzLiITLwWTqzqGAtW2oyOTq1A3pzqas';
echo decode($str)."\n";
?>
```

- 在线运行：http://www.dooccn.com/php5.6/

得到答案：nctf{rot13_and_base64_and_strrev}

## 待实现

- 判断题目特征：`crypto` `php` `OCR`
- OCR读取图片，得到加密函数，提交给FeatherDuster
- FeatherDuster自动生成解密函数，并运行得到结果
- 自动提交flag