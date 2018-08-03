## 题目

> base64全家桶全家桶全家桶！ 我怎么饿了。。。。。。 密文(解密前删除回车)：R1pDVE1NWlhHUTNETU4yQ0dZWkRNTUpYR00zREtNWldHTTJES1JSV0dJM0RDTlpUR1kyVEdNWlRHSTJVTU5SUkdaQ1RNTkJWSVk zREVOUlJHNFpUTU5KVEdFWlRNTjJF 

## 解答

### 思路

1. 全家桶 解释为使用了所有的base加密算法

   > base64、base32、base16可以分别编码转化8位字节为6位、5位、4位。16,32,64分别表示用多少个字符来编码，这里我注重介绍base64。Base64常用于在通常处理文本数据的场合，表示、传输、存储一些二进制数据。包括MIME的email，email via MIME,在XML中存储复杂数据。 
   >
   > 编码原理：Base64编码要求把3个8位字节转化为4个6位的字节，之后在6位的前面补两个0，形成8位一个字节的形式，6位2进制能表示的最大数是2的6次方是64，这也是为什么是64个字符(A-Z,a-z，0-9，+，/这64个编码字符，=号不属于编码字符，而是填充字符)的原因 

   > hex也称为base16，意思是使用16个可见字符来表示一个二进制数组，编码后数据大小将翻倍,因为1个字符需要用2个可见字符来表示。 

   使用的算法包括：base64，base32,base16(hex)

2. 可能的加密顺序：

   ```
   base64 --> base32 ->  hex
   ```

   三种加密方式的各种可能组合方式

##  待实现

- 在FeatherDuster中添加多种解密方式相结合的方法