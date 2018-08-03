### WxyVM

## 0x1 题目

> Wxy大神的VM
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

  puts("[WxyVM 0.0.1]");
  puts("input your flag:");
  scanf("%s", &byte_604B80);
  v4 = 1;
  sub_4005B6();
  if ( strlen(&byte_604B80) != 24 )
    v4 = 0;
  for ( i = 0; i <= 23; ++i )
  {
    if ( *(&byte_604B80 + i) != unk_601060[i] )
      v4 = 0;
  }
  if ( v4 )
    puts("correct");
  else
    puts("wrong");
  return 0LL;
}
```

关键是与unk_601060的比较，byte_604B80为输入值，sub_4005B6()应该对输入值进行了处理：

sub_4005B6():

```c
__int64 sub_4005B6()
{
  unsigned int v0; // ST04_4@3
  __int64 result; // rax@3
  signed int i; // [sp+0h] [bp-10h]@1
  char v3; // [sp+8h] [bp-8h]@3

  for ( i = 0; i <= 14999; i += 3 )
  {
    v0 = byte_6010C0[(signed __int64)i];
    v3 = byte_6010C0[(signed __int64)(i + 2)];
    result = v0;
    switch ( v0 )
    {
      case 1u:
        result = byte_6010C0[(signed __int64)(i + 1)];
        *(&byte_604B80 + result) += v3;
        break;
      case 2u:
        result = byte_6010C0[(signed __int64)(i + 1)];
        *(&byte_604B80 + result) -= v3;
        break;
      case 3u:
        result = byte_6010C0[(signed __int64)(i + 1)];
        *(&byte_604B80 + result) ^= v3;
        break;
      case 4u:
        result = byte_6010C0[(signed __int64)(i + 1)];
        *(&byte_604B80 + result) *= v3;
        break;
      case 5u:
        result = byte_6010C0[(signed __int64)(i + 1)];
        *(&byte_604B80 + result) ^= *(&byte_604B80 + byte_6010C0[(signed __int64)(i + 2)]);
        break;
      default:
        continue;
    }
  }
  return result;
}
```

### 0x2 编写逆算法

由于数组有15000个，所以可以写idapython的脚本，shift+F2在ida中运行

```python
from __future__ import print_function
s = []
for i in range(24):
  s.append(Byte(0x601060+4*i))
for i in range(14997,-1,-3):
  v0 = Byte(0x6010c0+i)
  v3 = Byte(0x6010c0+i+2)
  res = v0
  if v0==1:
    res=Byte(0x6010c0+i+1)
    s[res]-=v3
  elif v0==2:
    res=Byte(0x6010c0+i+1)
    s[res]+=v3
  elif v0==3:
    res=Byte(0x6010c0+i+1)
    s[res]^=v3
  elif v0==4:
    res=Byte(0x6010c0+i+1)
    s[res]/=v3
  else:
      res=Byte(0x6010c0+i+1)
      s[res]^=s[Byte(0x6010c0+i+2)]
for i in s:
  print(chr(i%256),end='')

```

最后输出的时候需要取模，这个在原程序中没有出现，还没弄清楚为什么没有影响

得到flag： `nctf{Embr4ce_Vm_j0in_R3}`

### 0x3 补充

> 链接：https://notwo1f.github.io/2018/05/04/%E5%8D%97%E9%82%AEre-writeup/

在上面的博文中提到了导出数组的方法：

1. 在要导出的字符串上按`*`（即`shift+8`)
2. 显示转换对话框，ida已经自动识别的数组长度为15000
3. `shift+e`提取数组，不需要全部选中

