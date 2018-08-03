# easy

## 题目

> 密文：bmN0Znt0aGlzX2lzX2Jhc2U2NF9lbmNvZGV9 

## 解答

### 思路

1. 对

   base64加密字符串字符包含如下：


   > A-Za-z0-9+/=


   题目密文符合上述要求，所以考虑使用base64进行解码

2. 解码后得到答案

### 解题步骤

- 工具：FeatherDuster

- 步骤：

  1. 创建文件

     ```shell
     echo bmN0Znt0aGlzX2lzX2Jhc2U2NF9lbmNvZGV9 > tmp.txt
     ```
     

  2. 将tmp.txt文件加入到FeatherDuster的sample中

     ```shell
     featherduster ./tmp.txt
     ```
     

  3. 进入featherduster的交互式界面，使用`samples`命令可查看是否已经将tmp.txt加入

     返回结果：

     > \------------------------------------------------------------
     > 'bmN0Znt0aGlzX2lzX2Jhc2U2NF9lbmNvZGV9\n'
     > \------------------------------------------------------------

  4. 使用`analyze`命令进行分析

     - 返回结果：

     > [+] Analyzing samples...
     > [+] Messages appear to be Base64 encoded, Base64 decoding and analyzing again.
     > [+] Messages may be encrypted with a stream cipher or simple XOR.

     - 根据结果提示，应该使用`stream cipher or simple XOR`进行解密

       这里选择模块`single_byte_xor `

  5. 使用命令`use single_byte_xor`设置解码模块

  6. 使用`run`命令进行解码，得到结果

     > [+] Running single-byte XOR brute force attack...
     >
     > Best candidate decryptions:
     > \----------------------------------------
     >
     > 'nctf{this_is_base64_encode}' (score: 2.905372)
     > '~sdvkdxycOycOrqcu&$Ou~s\x7ftum' (score: 4.481013)
     > 'lavdyvjkq]kq]`cqg46]glamfg\x7f' (score: 4.519879)

