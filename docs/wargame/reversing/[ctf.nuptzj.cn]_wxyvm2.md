### WxyVM2

## 0x1 题目

> Wxy大神的另一个VM
>
> 格式：`nctf{********}`

## 0x2 解题步骤

### 0x1 ida分析文件

ELF64位程序，使用ida64打开

看到关键的判断代码

```c
__int64 __fastcall main(__int64 a1, char **a2, char **a3)
{
  char v4; // [sp+Bh] [bp-5h]@1
  signed int i; // [sp+Ch] [bp-4h]@3

  puts("[WxyVM 0.0.2]");
  puts("input your flag:");
  scanf("%s", &byte_694100);
  v4 = 1;
  if ( strlen(&byte_694100) != 25 )
    v4 = 0;
xxxxxxxxxxxxx//中间省略了大量的混淆代码
 for ( i = 0; i <= 24; ++i )
  {
    if ( *(&byte_694100 + i) != dword_694060[i] )
      v4 = 0;
  }
  if ( v4 )
    puts("correct");
  else
    puts("wrong");
  return 0LL;
}
```

提取dword_694060处的值，需要修改当初的类型为数组：

```c
unsigned char ida_chars[] =
{
  0xC0, 0xFF, 0xFF, 0xFF, 0x85, 0xFF, 0xFF, 0xFF, 0xF9, 0xFF, 
  0xFF, 0xFF, 0x6C, 0x00, 0x00, 0x00, 0xE2, 0xFF, 0xFF, 0xFF, 
  0x14, 0x00, 0x00, 0x00, 0xBB, 0xFF, 0xFF, 0xFF, 0xE4, 0xFF, 
  0xFF, 0xFF, 0x0D, 0x00, 0x00, 0x00, 0x59, 0x00, 0x00, 0x00, 
  0x1C, 0x00, 0x00, 0x00, 0x23, 0x00, 0x00, 0x00, 0x88, 0xFF, 
  0xFF, 0xFF, 0x6E, 0x00, 0x00, 0x00, 0x9B, 0xFF, 0xFF, 0xFF, 
  0xCA, 0xFF, 0xFF, 0xFF, 0xBA, 0xFF, 0xFF, 0xFF, 0x5C, 0x00, 
  0x00, 0x00, 0x37, 0x00, 0x00, 0x00, 0xFF, 0xFF, 0xFF, 0xFF, 
  0x48, 0x00, 0x00, 0x00, 0xD8, 0xFF, 0xFF, 0xFF, 0x1F, 0x00, 
  0x00, 0x00, 0xAB, 0xFF, 0xFF, 0xFF, 0xA5, 0xFF, 0xFF, 0xFF
};
```

需要转换成32位的数据

```python
a = [0xffffffC0, 0xffffff85, 0xffffffF9, 0x6C, 0xffffffE2, 0x14, 0xffffffBB, 0xffffffe4, 0xd, 0x59, 0x1c, 0x23,
     0xffffff88, 0x6e, 0xffffff9b, 0xffffffca, 0xffffffba, 0x5c, 0x37, 0xffffffff, 0x48, 0xffffffd8, 0x1f, 0xffffffab,
     0xffffffa5]
```

混淆代码上含有大量无用的操作，分析得出，所有对dword的值的操作都是无效的，部分代码如下：

```c
dword_6941E0 ^= dword_6941DC;
  byte_694102 ^= 0xBu;
  byte_694100 ^= 0x5Eu;
  byte_694111 += 69;
  byte_694109 += 7;
--byte_694113;
```

- 将所有混淆代码保存为`vm2_2.c`
- 对于dword类型的操作：直接删除
- 对于byte类型的一般操作：
  - 提取byte_xxxxxx的最后两位，其为a字符串的数组索引值i
    - 16进制：取出0x和u之间的被操作数，转换成10进制，并与a[i]中的值进行逆操作
    - 10进制：取出被操作数，并于a[i]进行逆操作
- 对于自加自减的byte操作：
  - 提取byte_xxxxxx的最后两位，其为a字符串的数组索引值i
  - 根据操作符，进行自加自减操作
- 注意：语句遍历要从后往前

### 0x2 编写逆算法

参考[博客](https://blog.csdn.net/xiangshangbashaonian/article/details/78884187)，实现脚本，没有写完，先暂时如此，回头再来实

```python
#coding=UTF-8
operator = open('vm2_2.c','r').read()
# print operator
#operator为IDA反编译出的全部命令
o = operator.split(";")#分割命令形成列表
dword = "dword_"
operator_strip_dword = []
count = 1
for i in o:
    if dword in i:
        # print i
        continue
    else:
        # print i
        line = i.split(" ")
        if len(line)>3:
            last_2 = line[2][-2:]
            op = line[3]
            var2 = line[4]
            l = last_2 + op + var2
            print l
        # print last_2
            operator_strip_dword.append(l)
        elif len(line)>1:
            pass		#这里需要实现对++/--操作数的操作
            # print line
print operator_strip_dword
#以下为博客中的内容
a = [0xffffffC0, 0xffffff85, 0xffffffF9, 0x6C, 0xffffffE2, 0x14, 0xffffffBB, 0xffffffe4, 0xd, 0x59, 0x1c, 0x23,
     0xffffff88, 0x6e, 0xffffff9b, 0xffffffca, 0xffffffba, 0x5c, 0x37, 0xffffffff, 0x48, 0xffffffd8, 0x1f, 0xffffffab,
     0xffffffa5]

# f = open('vm2.c','r')
for b in f.readlines()[::-1]:
    if b[3] == '+':
        if 'x' in b:
            a[int(b[:2], 16)] -= int(b[8:], 16 if 'x' in b else 10)
        else:
            a[int(b[:2], 16)] -= int(b[6:], 10)
    if b[3] == '-':
        if 'x' in b:
            a[int(b[:2], 16)] += int(b[8:], 16)
        else:
            a[int(b[:2], 16)] += int(b[6:], 10)
    if b[3] == '^':
        if 'x' in b:
            a[int(b[:2], 16)] ^= int(b[8:], 16)
        else:
            a[int(b[:2], 16)] ^= int(b[6:], 10)
flag = ""
for i in a:
    flag += chr(i % 256)
print flag
```

得到flag： `nctf{th3_vM_w1th0ut_dAta}` 

