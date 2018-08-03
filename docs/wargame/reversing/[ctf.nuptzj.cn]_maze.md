### maze

## 0x1 题目

> maze 迷宫
>
> 格式：`nctf{********}`

## 0x2 解题步骤

### 0x1 ida分析文件

ELF64位程序，使用ida64打开

看到关键的判断代码

```c
__int64 __fastcall main(__int64 a1, char **a2, char **a3)
{
  signed __int64 position; // rbx@4
  signed int input_position; // eax@5
  bool v5; // bp@5
  bool v6; // al@8
  const char *outup_string; // rdi@19
  __int64 row; // [sp+0h] [bp-28h]@1

  row = 0LL;
  puts("Input flag:");
  scanf("%s", &input, 0LL);
  if ( strlen(&input) != 24 || strncmp(&input, "nctf{", 5uLL) || *(&byte_6010BF + 24) != 125 )
  {
LABEL_22:
    puts("Wrong flag!");
    exit(-1);
  }
  position = 5LL;
  if ( strlen(&input) - 1 > 5 )
  {
    while ( 1 )
    {
      input_position = *(&input + position);
      v5 = 0;
      if ( input_position > 78 )
      {
        input_position = (unsigned __int8)input_position;
        if ( (unsigned __int8)input_position == 'O' )
        {
          v6 = go_left((_DWORD *)&row + 1);
          goto LABEL_14;
        }
        if ( input_position == 'o' )
        {
          v6 = go_right((int *)&row + 1);
          goto LABEL_14;
        }
      }
      else
      {
        input_position = (unsigned __int8)input_position;
        if ( (unsigned __int8)input_position == '.' )
        {
          v6 = go_up(&row);
          goto LABEL_14;
        }
        if ( input_position == '0' )
        {
          v6 = go_down((int *)&row);
LABEL_14:
          v5 = v6;
          goto LABEL_15;
        }
      }
LABEL_15:
      if ( !(unsigned __int8)is_space_sharp((__int64)maze_string, SHIDWORD(row), row) )
        goto LABEL_22;
      if ( ++position >= strlen(&input) - 1 )
      {
        if ( v5 )
          break;
label_wrong:
        outup_string = "Wrong flag!";
        goto LABEL_21;
      }
    }
  }
  if ( *(&maze_string[8 * (signed int)row] + SHIDWORD(row)) != '#' )
    goto label_wrong;
  outup_string = "Congratulations!";
LABEL_21:
  puts(outup_string);
  return 0LL;
}
```

- 其中许多关键变量，我进行了重新命名，使程序的可读性更好
- 参考链接：https://blog.csdn.net/whklhhhh/article/details/74852452（其中有更详细的解释）
- 参考链接2：http://n3k0sec.top/2017/12/11/%E5%8D%97%E9%82%AEReverse/#maze（这里有pythonida的脚本）





### 0x2 pythonida中打印迷宫

```python
for i in range(8):
    for j in range(8):
        print chr(Byte(0x601060+j+i*8)),
    print
```
输出：

```
    * * * * * *
*       *     *
* * *   *   * *
* *     *   * *
*     * #     *
* *   * * *   *
* *           *
* * * * * * * *

```

### 0x3 走迷宫

```
!r******
*drr*!!*
***d*!**
**ld*!**
*!d*llu*
**d***u*
**drrrr*
********
```

r:right l:left d:down u:up

- 得到字符串：rdrrddldddrrrruull

- 使用题目要求的字符替换，规则如下：

  r o 
  l O
  u .
  d 0

- 得到flag： `nctf{o0oo00O000oooo..OO} `

  **提交flag，提示错误，在本地运行，也发现错误，具体原因待查**

  从[博文](https://blog.csdn.net/qq_31344951/article/details/76458862)中找到了flag：`nctf{o0oo00O000oooo..OO}`，提交居然成功了，对比一下，发现和之前做出来的一样，难道是复制粘贴的问题？还是平台的故障？

  不过在本地运行程序，给出的答案依然是错的，以后可以试试动态调试，看看哪里有问题

