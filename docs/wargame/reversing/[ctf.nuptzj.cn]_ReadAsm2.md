### ReadAsm2

## 0x1 题目

> 读汇编是逆向基本功。
>
> 给出的文件是func函数的汇编
> main函数如下
> 输出的结果即为flag，格式为`flag{**********}`，请连flag{}一起提交
>
> 编译环境为linux gcc x86-64
> 调用约定为System V AMD64 ABI
> **请不要利用汇编器，IDA等工具。。这里考的就是读汇编与推算汇编结果的能力**

```c
int main(int argc, char const *argv[])
{
  char input[] = {0x0,  0x67, 0x6e, 0x62, 0x63, 0x7e, 0x74, 0x62, 0x69, 0x6d,
                  0x55, 0x6a, 0x7f, 0x60, 0x51, 0x66, 0x63, 0x4e, 0x66, 0x7b,
                  0x71, 0x4a, 0x74, 0x76, 0x6b, 0x70, 0x79, 0x66 , 0x1c};
  func(input, 28);
  printf("%s\n",input+1);
  return 0;
}
```

> 参考资料: <https://github.com/veficos/reverse-engineering-for-beginners> 
>
> 《汇编语言》王爽 
>
> 《C 反汇编与逆向分析技术揭秘》 

## 0x2 解题步骤

### 0x1 阅读.2asm

- 参考：https://blog.csdn.net/github_35681219/article/details/52973809

```asm
00000000004004e6 <func>:
  4004e6: 55                    push   rbp
  4004e7: 48 89 e5              mov    rbp,rsp
  4004ea: 48 89 7d e8           mov    QWORD PTR [rbp-0x18],rdi		;0x18=rdi(input)
  4004ee: 89 75 e4              mov    DWORD PTR [rbp-0x1c],esi		;0x1c=esi(28)
  4004f1: c7 45 fc 01 00 00 00  mov    DWORD PTR [rbp-0x4],0x1		;0x4(count)=1
  4004f8: eb 28                 jmp    400522 <func+0x3c>
  4004fa: 8b 45 fc              mov    eax,DWORD PTR [rbp-0x4]		
  4004fd: 48 63 d0              movsxd rdx,eax
  400500: 48 8b 45 e8           mov    rax,QWORD PTR [rbp-0x18]		;rax=input[0]	
  400504: 48 01 d0              add    rax,rdx						;rax = input [count]
  400507: 8b 55 fc              mov    edx,DWORD PTR [rbp-0x4]
  40050a: 48 63 ca              movsxd rcx,edx
  40050d: 48 8b 55 e8           mov    rdx,QWORD PTR [rbp-0x18]
  400511: 48 01 ca              add    rdx,rcx						;rdx = input[count]
  400514: 0f b6 0a              movzx  ecx,BYTE PTR [rdx]			
  400517: 8b 55 fc              mov    edx,DWORD PTR [rbp-0x4]
  40051a: 31 ca                 xor    edx,ecx						;int(byte(count))^int(byte(input + 																	   count)) 
  40051c: 88 10                 mov    BYTE PTR [rax],dl			;byte(input + count) = 																	byte(int(byte(count))^int(byte(input + count)))
  															;更好的形式：input[count]^=count
  40051e: 83 45 fc 01           add    DWORD PTR [rbp-0x4],0x1		;count++
  400522: 8b 45 fc              mov    eax,DWORD PTR [rbp-0x4]		
  400525: 3b 45 e4              cmp    eax,DWORD PTR [rbp-0x1c]		;if count<=int(28)
  400528: 7e d0                 jle    4004fa <func+0x14>
  40052a: 90                    nop
  40052b: 5d                    pop    rbp
  40052c: c3                    ret
```

### 0x2 写出pyton代码

```python
input=[0x0,0x67, 0x6e, 0x62, 0x63, 0x7e, 0x74, 0x62,0x69, 0x6d, 0x55, 0x6a, 0x7f, 0x60, 0x51, 0x66,0x63, 0x4e, 0x66, 0x7b, 0x71, 0x4a, 0x74, 0x76,0x6b, 0x70, 0x79, 0x66 ,0x1c]
count = 28

res=''
# print input[28]
for i in xrange(count):

    # print input[i+1]
    # print chr(input[i+1])
    tmp = chr(input[i+1]^(i+1))
    print tmp
    res+=tmp
    # print res
print res
```

跑出答案：flag{read_asm_is_the_basic}

**需要注意的：input开始运算的位置为1**