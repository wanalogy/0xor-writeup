# 丘比龙de女神

## 题目

> http://ctf.nuptzj.cn/challenges#%E4%B8%98%E6%AF%94%E9%BE%99De%E5%A5%B3%E7%A5%9E
>
> 丘比龙是丘比特的弟弟，由于吃了太多的甜甜圈导致他飞不动了！
>
> 没错 里面隐藏了一张女神的照片
> flag是照片文件的md5值(小写)
> 记住加上flag{}
>
> ![图种](http://ctf.nuptsast.com/static/uploads/58d386b9a657e367f337aab9a751d37f/gif)

## 解答思路

1. binwalk查看隐藏信息
2. 010 edtior查看文件结构并修复
3. 注意文件的开始位置


## 解题步骤

#### 0x1 题目解读

1. 该gif中隐藏有一张女神(**nvshen**)的照片
2. 计算该图片的flag值，加上flag{}之后提交

#### 0x2 binwalk分析

```shell
binwalk pikaqiu.gif 
```

输出：

> DECIMAL       HEXADECIMAL     DESCRIPTION
>
> 0             0x0             GIF image data, version "87a", 100 x 100
> 115088        0x1C190         End of Zip archive

看出该gif中隐藏有zip文件，但是只有结尾，文件头需要修复

#### 0x2 010 editor查看

010 editor打开该gif，模板结果如下：

![1531985712375](D:\all\sec\note\mkdocs\ctf_writeup\docs\_images\1531985712375.png)

可以看出，该文件的结尾在`D8C2`处，可以观察到其后有`love`和`nvshen.jpg`两个有意义的词，所以，可以猜测从love开始到文件结尾应该是隐藏的zip文件（这里收集关键词 love 和 nvshen）

#### 0x3 导出并修复该zip文件

1. 在`6c`处选择`selection start`，在结尾处选择`selection end`,file中选择`save selection`，保存成zip文件`pikaqiu_D8C3h_E8E3h.zip`
2. 010editor打开该文件，然后将文件头修改为`504b0304`，保存后使用zip解压缩软件解压，发现需要输入密码，尝试输入`love`，成功

#### 0x4 md5sum计算md5

使用md5sum计算，加上flag{}之后提交

## 自动化解析

- 判断题目特征：`web` `simple` `webpage_source`
- 设定题目难度等级`level`为simple，采用查看源代码的方式(将源代码中的字段隐藏属性去掉，或者将注释改为可见)，依据常见的flag格式，匹配到`flag`，并对其赋值，自动提交

