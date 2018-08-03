## 题目

> 同性真爱，异性相吸都是假的！
> （题目要求，我是直的）
>
> 解密压缩文件里的内容
>
> TIPS:
> 1.xor
> 2.hex2binary
> 3.len(bin(miwen))==len(bin(mingwen))

> 密文：lovelovelovelovelovelovelovelove
>
> 明文：

## 解题

### 思路

1. xor两个文件的ascii码

### 步骤

- python代码实现

  ```python
  ens = open('D:/all/sec/exercise/gongxin/nanyou/biubiubiu/mingwen.txt'，'r').read()
  des = open('D:/all/sec/exercise/gongxin/nanyou/biubiubiu/miwen.txt'，'r').read()
  flag = ''
  for i in range(len(ens)):
       flag = flag + chr(ord(des[i])^ord(ens[i]))
  print flag
  ```

  

## 待实现

- 实现该题目的自动答题通用性较低，可以考虑实现chrome的**python console**，自动获取压缩文件，并解压到指定题目的目录下，然后自动生成读取文件的代码

