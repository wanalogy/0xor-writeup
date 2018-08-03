# reversing misc

## 0x01 hex文件转换

### 0x1 xxd

```shell
xxd [options] [infile [outfile]]
xxd -r[evert] [options] [infile [outfile]]
```

- 功能

  xxd 命令用于用二进制或十六进制显示文件的内容，如果没有指定outfile参数，则把结果显示在屏幕上，如果指定了outfile则把结果输出到 outfile中；如果infile参数为 – ，或没有指定infile参数，则默认从标准输入读入。

- 常用选项

**-b**

> 转到 比特(二进制 数字) 模式, 而不是十六进制模式。在这种模式下, 每个字符被表示成八个 0/1 的数字，而不是一般的十六进制形式。每一行都以一个用十六进制形式表示的行号，后面是ascii (或者 ebcdic) 形式开头。命令行选项 -r, -p 在这个模式下不起作用。

**-l N**

只输出 N 个字符。

**-p**

以 postscript的连续十六进制转储输出，这也叫做纯十六进制转储。

**-r**

> 逆向操作: 把xxd的十六进制输出内容转换回原文件的二进制内容。

**-seek offset**

>  用在 -r 之后: 会在当前档桉的 偏移量上增加 .

**-s \[+][-]seek**

> 从infile的绝对或者相对偏移量 开始. + 表示相对于标 输入当前的位置 (如果不 标准输入就没有意义了). – 表示**从档桉末尾** (如果和 + 连用: 从标准输入当前位置) 向前数一些字符, 从那个地方开始. 如果没有 -s 选项, xxd 从 当前位置开始.

**-u**

十六进制输出时使用大写字母，默认是小写字母。

使用范例：(摘自xxd man page)

1. 显示 file 除了前 三行 (十六进制 的 0×30) 的 所有 内容.

```shell
$ xxd -s 0×30 file
```

1. 显示 file 最后 三行 (十六进制 的 0×30) 的 所有 内容.

```shell
$ xxd -s -0×30 file
```

1. 显示 120 个 字符, 每行 20 个 字符, 连续 显示.

```shell
$ xxd -l 120 -ps -c 20 xxd.1
```

> 2e544820585844203120224d616e75616c207061
> 676520666f7220787864220a2e5c220a2e5c2220
> 32317374204d617920313939360a2e5c22204d61
> 6e207061676520617574686f723a0a2e5c222020
> 2020546f6e79204e7567656e74203c746f6e7940
> 7363746e7567656e2e7070702e67752e6564752e

1. 显示 120 个 字符, 每行 12 个 字符.

```shell
$ xxd -l 120 -c 12 xxd.1
```

> 0000000: 2e54 4820 5858 4420 3120 224d .TH XXD 1 “M
> 000000c: 616e 7561 6c20 7061 6765 2066 anual page f
> 0000018: 6f72 2078 7864 220a 2e5c 220a or xxd”..\”.
> 0000024: 2e5c 2220 3231 7374 204d 6179 .\” 21st May
> 0000030: 2031 3939 360a 2e5c 2220 4d61 1996..\” Ma
> 000003c: 6e20 7061 6765 2061 7574 686f n page autho
> 0000048: 723a 0a2e 5c22 2020 2020 546f r:..\” To
> 0000054: 6e79 204e 7567 656e 7420 3c74 ny Nugent 
> 0000060: 6f6e 7940 7363 746e 7567 656e ony@sctnugen
> 000006c: 2e70 7070 2e67 752e 6564 752e .ppp.gu.edu.

1. 只 显示 xxd.1 中 的 日期.

```shell
$ xxd -s 0×28 -l 12 -c 12 xxd.1
```

> 0000028: 3231 7374 204d 6179 2031 3939 21st May 199

1. 把 input_file 考到 output_file 并 在 前面 增加 100 个 字节的 0×00.

```shell
$ xxd input_file | xxd -r -s 100 > output_file
```

1. 给 档桉 xxd.1 中的 日期 打 补钉.

```shell
$ echo ‘0000029: 3574 68′ | xxd -r – xxd.1

$ xxd -s 0×28 -l 12 -c 12 xxd.1
```

> 0000028: 3235 7468 204d 6179 2031 3939 25th May 199

1. 建立 一个 65537 字节的 档桉, 所有 字节 都是 0×00, 除了 最后 一个 字节 是 ‘A’ (十六进制 0×41).

```shell
$ echo ‘010000: 41′ | xxd -r > file
```

1. 打开 autoskip, 显示 上例 中 建立的 档桉.

```shell
$ xxd -a -c 12 file
```

> 0000000: 0000 0000 0000 0000 0000 0000 …………
> *
> 000fffc: 0000 0000 40 ….A

1. 建立 一个 只 含有 一个 ‘A’ 的 档桉. ‘-r -s’ 后面 的数字 同 档桉 中的 行号 相 抵消; 结果是 开头 的 字节 被跳过了.

```shell
$ echo ‘010000: 41′ | xxd -r -s -0×10000 > file
```

1. 从 串行线 中 读入 一个个的 单独的 字符.

```shell
$ xxd -c1 < /dev/term/b &

$ stty < /dev/term/b -echo -opost -isig -icanon min 1

$ echo -n foo > /dev/term/b

```

## 0x03 系统辨认

### 0x01 EFI格式

- 参考链接：https://www.ibm.com/developerworks/cn/linux/l-excutff/index.html

1. 文件格式

   - 二进制文件
   - 可执行文件
   - 目标代码
   - 共享库
   - 核心转储格式

2. ELF组成

   - ELF文件的总体布局：

   | ELF header(ELF头部)               |
   | --------------------------------- |
   | Program header table(程序头表)    |
   | Segment1（段1）                   |
   | Segment2（段2）                   |
   | ………                               |
   | Sengmentn（段n）                  |
   | Setion header table(节头表，可选) |

   **只有ELF的头部是固定的，其余部分都有头部参数指定**

   - ELF文件含义

     ```c
     typedef struct
     {
             unsigned char e_ident[EI_NIDENT];     /* 魔数和相关信息 */
             Elf32_Half    e_type;                 /* 目标文件类型 */
             Elf32_Half    e_machine;              /* 硬件体系 */
             Elf32_Word    e_version;              /* 目标文件版本 */
             Elf32_Addr    e_entry;                /* 程序进入点 */
             Elf32_Off     e_phoff;                /* 程序头部偏移量 */
             Elf32_Off     e_shoff;                /* 节头部偏移量 */
             Elf32_Word    e_flags;                /* 处理器特定标志 */
             Elf32_Half    e_ehsize;               /* ELF头部长度 */
             Elf32_Half    e_phentsize;            /* 程序头部中一个条目的长度 */
             Elf32_Half    e_phnum;                /* 程序头部条目个数  */
             Elf32_Half    e_shentsize;            /* 节头部中一个条目的长度 */
             Elf32_Half    e_shnum;                /* 节头部条目个数 */
             Elf32_Half    e_shstrndx;             /* 节头部字符表索引 */
     } Elf32_Ehdr;
     ```

3. 识别文件格式

   `file`:识别处文件的基本格式

   `readelf`:如果无法运行，则需要具体查看文件头信息

   ```shell
   readelf 文件名
   ```

## 0x04 docker使用

### 0x01 service-start-limit-hit报错

> ```json
> {
>     "registry-mirrors": [
>         "加速地址"
>     ],
>     "insecure-registries": []
> }
> ```

加速需要加上外面的`"`,否则就会报错

### 0x02 Docker 国内镜像

网易加速器：http://hub-mirror.c.163.com

官方中国加速器：https://registry.docker-cn.com

ustc的镜像：https://docker.mirrors.ustc.edu.cn

daocloud：https://www.daocloud.io/mirror#accelerator-doc（注册后使用）

### 0x03 risc-v虚拟环境搭建

```shell
docker run --privileged -v /home:/home -it sorear/fedora-riscv-wipv
```

### 0x04 安装出错

> FATAL: kernel too old
>
> Starting architecture emulation failed.  binfmt_misc configuration requires
> true root privileges, and is not effective in sandboxed root environments
> such as is provided by Docker by default. You will need to run the following
> command as root on the Docker host machine:
>
> ```shell
>  echo ':riscv64:M::\x7f\x45\x4c\x46\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\xf3\x00:\xff\xff\xff\xff\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\xff\xff:/usr/bin/qemu-riscv64:' > /proc/sys/fs/binfmt_misc/register
> ```
>
> It is also possible to run this container with --privileged; this might be
> useful for Docker on Mac or Docker on Windows.

解决办法未知