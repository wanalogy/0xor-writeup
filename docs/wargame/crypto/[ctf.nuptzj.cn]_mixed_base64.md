## 题目

> 密文：http://ctf.nuptzj.cn/static/uploads/3e5c0c7045f2a81363a45d97d69911e3/code.txt

base64编码的图片，显示python加密函数

## 解答思路

1. 编写解密函数decode，采用爆破的方法
2. 解密相应字符串

## 解题步骤

- 解密方法：

```python
from base64 import *
import urllib2
url = "http://ctf.nuptzj.cn/static/uploads/3e5c0c7045f2a81363a45d97d69911e3/code.txt"
s = urllib2.urlopen(url).read().replace("\n","")
result={
    '16':lambda x:b16decode(x),
    '32':lambda x:b32decode(x),
    '64':lambda x:b64decode(x)
    }
for i_1 in ['16','32','64']:
    for i_2 in ['16','32','64']:
        for i_3 in ['16','32','64']:
            for i_4 in ['16','32','64']:
                for i_5 in ['16','32','64']:
                    for i_6 in ['16','32','64']:
                        for i_7 in ['16','32','64']:
                            for i_8 in ['16','32','64']:
                                for i_9 in ['16','32','64']:
                                    for i_10 in ['16','32','64']:
                                        try:
                                            print result[i_10](result[i_9](result[i_8](result[i_7](result[i_6](result[i_5](result[i_4](result[i_3](result[i_2](result[i_1](s))))))))))
                                        except:
                                            continue
```

得到答案：nctf{random_mixed_base64_encode}

## 待实现

- 判断题目特征：`crypto` `python` ` OCR`
- OCR读取图片，得到加密函数，提交给FeatherDuster
- FeatherDuster自动生成解密函数，并运行得到结果
- 自动提交flag
- 此题如何自动生成解密函数，是个难点，暂时解决不了
- 如何使用正则判断编码属于base64、base32、base16，需要进一步考虑，这样在爆破过程中，可以减少循环次数

