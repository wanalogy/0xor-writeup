## 题目

> http://ctf.nuptzj.cn/challenges#Vigenere
>
> 1.The encode pragram is given; 2.Do u no [index of coincidence ](https://en.wikipedia.org/wiki/Index_of_coincidence)？ 3.The key is last 6 words of the plain text(with "nctf{}" when submitted, also without any interpunction) 

## 解答思路

>  参考：https://www.tr0y.wang/2016/09/26/NyouCTF/

1. 题目的类型为简单的xor型vigenere加密

   题目给出了加密方式，长度在1到13之间，并且对每个字符的ascii码的十六进制数按位异或，得到密文

2. 猜测密钥可能的位数，具体看参考链接

3. 暴力穷举，密文和密钥异或得到最多可打印字符的位作为当前位的密钥位

4. 本题的参考链接中自己用python实现了破解，但在[augusto-ludtke](https://github.com/augusto-ludtke/xorbreak)中，给出了更有效的解决办法，而且通用性更高，具体用法参看其readme

## 解题步骤

- 解密方法：

```python
# -*- coding: cp936 -*-
import re,pprint
from Crypto.Util import strxor
C = 'F96DE8C227A259C87EE1DA2AED57C93FE5DA36ED4EC87EF2C63AAE5B9A7EFFD673BE4ACF7BE8923CAB1ECE7AF2DA3DA44FCF7AE29235A24C963FF0DF3CA3599A70E5DA36BF1ECE77F8DC34BE129A6CF4D126BF5B9A7CFEDF3EB850D37CF0C63AA2509A76FF9227A55B9A6FE3D720A850D97AB1DD35ED5FCE6BF0D138A84CC931B1F121B44ECE70F6C032BD56C33FF9D320ED5CDF7AFF9226BE5BDE3FF7DD21ED56CF71F5C036A94D963FF8D473A351CE3FE5DA3CB84DDB71F5C17FED51DC3FE8D732BF4D963FF3C727ED4AC87EF5DB27A451D47EFD9230BF47CA6BFEC12ABE4ADF72E29224A84CDF3FF5D720A459D47AF59232A35A9A7AE7D33FB85FCE7AF5923AA31EDB3FF7D33ABF52C33FF0D673A551D93FFCD33DA35BC831B1F43CBF1EDF67F0DF23A15B963FE5DA36ED68D378F4DC36BF5B9A7AFFD121B44ECE76FEDC73BE5DD27AFCD773BA5FC93FE5DA3CB859D26BB1C63CED5CDF3FE2D730B84CDF3FF7DD21ED5ADF7CF0D636BE1EDB79E5D721ED57CE3FE6D320ED57D469F4DC27A85A963FF3C727ED49DF3FFFDD24ED55D470E69E73AC50DE3FE5DA3ABE1EDF67F4C030A44DDF3FF5D73EA250C96BE3D327A84D963FE5DA32B91ED36BB1D132A31ED87AB1D021A255DF71B1C436BF479A7AF0C13AA14794'
gcd = []
for z in set(re.findall(r'(.{2})',C)): #2个一组
    loc = [0]+[s.start() for s in re.finditer(z, C)]
    gcd += [j-i for i,j in zip(loc,loc[1:])]
#分别计算能被1-14整除的距离数
pprint.pprint(sorted([(j,i) for (i,j) in [[i, sum([j for j in gcd if j%i==0 and j])] for i in range(1,14)]], reverse = True))
#7较难被整除, 在结果里却排名靠前, 说明Keylen很有可能是7
Keylen = 7
Key = ['*'] * Keylen
KeyStr = ['*'] * Keylen

Csplit = [re.findall(r'(.{2})',z) for z in re.findall(r'(.{'+str(Keylen*2)+'})',C)]


# pprint.pprint(Csplit)
pprint.pprint(zip(*Csplit))
pprint.pprint(map(list,[zip(*Csplit)])[0])
print (zip(*Csplit))
print (map(list,[zip(*Csplit)])[0])
Transpose = zip(*Csplit)
Transpose = map(list,[zip(*Csplit)])[0]

for i in range(Keylen):
    m = 0
    for k in range(255):
        score = len(re.findall(r'[a-zA-Z ,\.;?!:]', ''.join([strxor.strxor(c.decode('hex'), chr(k)) for c in Transpose[i]])))
        if m < score:
            m = score
            Key[i] = chr(k).encode('hex')
            KeyStr[i] = chr(k)
# keyStr = ''.join(Key)
print 'key:', ''.join(Key)
print 'key(ascii):', ''.join(KeyStr)
print 'key(ascii):', str(''.join(Key).decode('hex'))

Key = str(''.join(Key).decode('hex'))*100
print 'M:',strxor.strxor(Key[:len(C.decode('hex'))],C.decode('hex'))
```



## 待实现

- 判断题目特征：`crypto` `vigenere` `simple_xor`
- 赋值变量`cipher=`(密文)，`cipher_method`(加密方式，这里是`vigenere` )，`tools`(采用的工具，这里是xorbreak)
- 给出chrome的linux shell界面，并自动填充命令模板，执行猜解长度操作
- 填充猜解密钥的命令模板，填充最优猜解项，自动执行，或者等待用户修改后执行
- 用户确认结果正确后，根据题目要求，自动获取需要提交的flag，经过必要的修改（类似添加nctf{}之类的操作），自动提交，或者等待用户修改后提交

