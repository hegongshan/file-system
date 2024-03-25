



### truncate命令

#### 命令说明

```shell
truncate OPTION... FILE...
```

如果文件不存在，则会创建该文件。

常用选项：

* `-c`, `--no-create`：不要创建文件。

* `-s`, `--size=SIZE`：将文件的大小设置或调整为SIZE字节。

  SIZE可以指定单位，`K,M,G,T,P,E,Z,Y`（1024次幂），或者`KB,MB,...`（1000次幂）

  可选的前缀字符：

  `+`（扩展）：将文件大小扩展SIZE

  `-`（减少）：将文件大小减少SIZE

  `<`（最大）：如果文件大小>SIZE，则将文件截断为指定大小；如果文件大小<=SIZE，则不会对文件进行任何更改。

  `>`（最小）：如果文件大小<SIZE，则将文件扩展为指定大小；如果文件大小>=SIZE，则不会对文件进行任何更改。

  `/`：向下舍入为SIZE的倍数

  `%`：向上舍入为SIZE的倍数

* `-o`, `--io-blocks`：将SIZE作为IO块的数量，而不是字节数。

* `-r`, `--reference=RFILE`：根据参考文件`RFILE`的大小来调整目标文件的大小。

如果同时使用了选项`--size`，那么SIZE必须是一个相对值，即必须使用前缀字符，否则会报如下错误：

```shell
truncate: you must specify a relative ‘--size’ with ‘--reference’
```

如果不指定SIZE，则将目标文件的大小设置为参考文件的大小。

#### 示例

1. 将空文件扩展为1024K

```shell
$ touch test.txt
$ truncate --size 1024K test.txt
$ ls -l test.txt
-rw-r--r-- 1 root root 1048576 Mar 24 14:21 test.txt
```

2. 将文件大小增加1024K

```shell
$ truncate --size +1024K test.txt
$ ls -l test.txt
-rw-r--r-- 1 root root 2097152 Mar 24 14:25 test.txt
```

3. 将文件的最小大小设置为4M

```shell
$ truncate --size \>4M test.txt
$ ls -lh test.txt 
-rw-r--r-- 1 root root 4.0M Mar 24 14:28 test.txt
```

注意：`>`和`<`是Shell中用于重定向的关键字。

如果要在选项`--size`中使用前缀`>`和`<`，有以下两种方法：

* 方法一：使用转义字符`\`对`>`和`<`进行转义，例如，`truncate --size \>4M test.txt`；
* 方法二：使用双引号包裹，例如，`truncate --size ">4M" test.txt`或者`truncate --size ">"4M test.txt `。

4. 将文件的大小设置为10个IO块的大小（通常情况下，Linux中的块大小为4K）

```shell
$ truncate --io-blocks --size 10 test.txt
$ ls -lh test.txt 
-rw-r--r-- 1 root root 40K Mar 24 14:31 test.txt
```

5. 将文件截断为0字节

```shell
$ truncate --size 0 test.txt
$ ls -l test.txt 
-rw-r--r-- 1 root root 0 Mar 24 14:32 test.txt
```

6. 当参考文件test_r.txt的大小为2M时，将test.txt的大小扩展1M

```shell
$ truncate --size 2M test_ref.txt
$ ls -lh test_ref.txt 
-rw-r--r-- 1 root root 2.0M Mar 25 02:19 test_ref.txt
$ truncate --reference test_ref.txt --size +1M test.txt
$ ls -lh test.txt 
-rw-r--r-- 1 root root 3.0M Mar 25 02:19 test.txt
```

7. 当参考文件test_r.txt的大小为2M时，修改test.txt的大小为参考文件的大小

```shell
$ truncate --reference test_ref.txt test.txt
$ ls -lh test.txt 
-rw-r--r-- 1 root root 2.0M Mar 25 02:23 test.txt
```

8. 查看test.txt占用的磁盘空间大小

```shell
$ du -h test.txt 
0	test.txt
```

**truncate只会改变文件的size属性，并不会分配磁盘空间**。我们可以向test.txt写入一些内容，再查看其占用的磁盘空间大小。

```shell
$ echo "hello world!" >> test.txt
$ ls -lh test.txt 
-rw-r--r-- 1 root root 2.1M Mar 25 03:55 test.txt
$ du -h test.txt 
4.0K	test.txt
```

在Linux中，文件系统使用块作为磁盘空间的分配单位。为了存储写入的内容，文件系统将会为test.txt分配一个块。因此，du命令输出其占用了4K的磁盘空间。实际上，test.txt只使用了其中的13个字节。

### 系统调用truncate/ftruncate

#### 函数说明

函数签名如下所示：

```c
#include <unistd.h>
#include <sys/types.h>

int truncate(const char *path, off_t length);
int ftruncate(int fd, off_t length);
```

参数说明：

* `path`指定文件的路径名，`length`表示字节数。

当使用上述系统调用时，`path`或`fd`引用的文件必须已经存在。

#### 示例

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(int argc, char *argv[])
{
    if (argc < 3) {
        printf("arguments error");
        exit(1);
    }

    if (truncate(argv[1], atoi(argv[2])) != 0) {
        perror("error");
    }
    return 0;
}
```

1. 编译上述代码

```shell
$ gcc test_truncate.c -o test_truncate
```

2. 创建测试文件

```shell
$ touch test.log
```

3. 扩展为1K

```shell
$ ./test_truncate test.log 1024
$ ls -lh test.log
-rw-r--r-- 1 root root 1.0K Mar 25 02:43 test.log
```

4. 截断为32字节

```shell
$ ./test_truncate test.log 32
$ ls -lh test.log
-rw-r--r-- 1 root root 32 Mar 25 02:45 test.log
```

