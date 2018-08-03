# 图种

## 题目

> http://ctf.nuptzj.cn/challenges#%E5%9B%BE%E7%A7%8D
>
> flag是动态图最后一句话的拼音首字母 加上nctf{}  
>
> ![图种](http://ctf.nuptsast.com/static/uploads/1cf33bb62640e61a1a5e3892cb348b04/555.gif)

## 解答思路

1. binwalk查看隐藏信息
2. foremost提取隐藏信息


## 解题步骤

#### 0x1 binwalk分析

```shell
binwalk  555.gif
```

输出：

> DECIMAL       HEXADECIMAL     DESCRIPTION
>
> 0             0x0             GIF image data, version "87a", 100 x 100
> 58000         0xE290          Zip archive data, at least v2.0 to extract, compressed size: 10882, uncompressed size: 15579, name: 233333.gif
> 69014         0x10D96         End of Zip archive

说明存在隐藏的zip文件

#### 0x2 foremost提取

```shell
foremost -i 555.gif 
```

输出：

> File: 555.gif
> Start: Wed Jul 18 23:28:07 2018
> Length: 67 KB (69036 bytes)
>
> Num	 Name (bs=512)	       Size	 File Offset	 Comment 
>
> 0:	00000000.gif 	      56 KB 	          0 	  (100 x 100)
> 1:	00000113.zip 	      10 KB 	      58000 	 
> Finish: Wed Jul 18 23:28:07 2018
>
> 2 FILES EXTRACTED	
> gif:= 1
>
> zip:= 1

#### 0x3 解压缩zip文件

```shell
unzip output/zip/00000113.zip
```

输出：

> 233333.gif

#### 0x4 查看gif文件

```shell
eog 233333.gif
```

输出：

![23333.gif](D:\all\sec\note\mkdocs\ctf_writeup\docs\_images\233333.gif)*

- 根据题目给出的提示，得到最后一句话的拼音首字母：dssdcmkw
- 拼接后提交：`nctf{dssdcmkw}`

## 自动化解析

- 判断题目特征：`web` `simple` `webpage_source`
- 设定题目难度等级`level`为simple，采用查看源代码的方式(将源代码中的字段隐藏属性去掉，或者将注释改为可见)，依据常见的flag格式，匹配到`flag`，并对其赋值，自动提交

