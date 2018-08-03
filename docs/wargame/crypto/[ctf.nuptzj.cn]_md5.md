## 题目

> python大法好！
> 这里有一段丢失的md5密文
> e9032???da???08????911513?0???a2
> 要求你还原出他并且加上nctf{}提交
>
> 已知线索 明文为： TASC?O3RJMV?WDJKX?ZM

## 解题

### 思路

1. 遍历明文中每个未知的字符，生成对应的md5值，与给出的MD5值进行比较，得到答案

### 步骤

- python代码实现

  ```python
  import hashlib
  plaintext = "TASC?O3RJMV?WDJKX?ZM"
  ciphertext = "e9032???da???08????911513?0???a2"
  
  for i in range(32,127):
      for j in range(32,127):
          for k in range(32,127):
              m = hashlib.md5()
              str = 'TASC'+chr(i)+'O3RJMV'+chr(j)+'WDJKX'+chr(k)+'ZM'
              m.update(str)
              des = m.hexdigest()
              if 'e9032' in des and 'da' in des and '08' in des and '911513' in des and 'a2' in des:
                  print str
                  print des
  ```

  

## 待实现

- 实现该题目的自动答题通用性较低，可以考虑实现chrome的**python console**，自动读取这两个字符串
- 自动生成样板代码，提供参考，加快解题速度

