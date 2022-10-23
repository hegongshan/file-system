dd是Unix和Unix-like系统中的一个命令，用于转换和复制文件（convert and copy, cc）。然而，cc和C Compiler冲突了，故取名dd。

### 选项

if: input file

of: output file

bs:  block size，输入和输出的块大小

ibs: input block size，输入的块大小

obs: output block size，输出的块大小

count=N: 从输入文件中复制N个块

### 输出格式

```shell
完整输入块数+部分输入块数
完整输出块数+部分输出块数
```

```shell
hegongshan@hgs:~/Desktop$ sudo dd if=/dev/sda5 of=/dev/null bs=512M count=4 
4+0 records in
4+0 records out
2147483648 bytes (2.1 GB, 2.0 GiB) copied, 3.79005 s, 567 MB/s
```

