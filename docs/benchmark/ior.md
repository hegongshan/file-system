数据带宽测试之IOR

### 安装IOR

1.执行引导程序

```shell
$ ./bootstrap
./bootstrap:行3: autoreconf: 未找到命令
```

解决办法：

```shell
yum install -y autoconf automake libtool
```

2.执行配置程序

```shell
./configure
```

3.编译

```shell
make -j5
```

编译完成后，可执行程序`ior`位于`${IOR}/src`目录下

4.安装（可选）

```shell
make install
```

### 常用选项

-i *N*: 重复几次试验。

-N *N*: numTasks

> number of tasks that should participate in the test. 0 denotes all tasks. (default: 0)

-s *N*: segmentCount

> number of segments in file, where a segment is a contiguous chunk of data accessed by multiple clients each writing/reading their own contiguous data (blocks) (default: 1).

-b *N*: blockSize – contiguous bytes to write per task (e.g.: 8, 4k, 2m, 1g)

> size (in bytes) of a contiguous chunk of data accessed by a single client. It is comprised of one or more transfers (default: 1048576)

-t *N*: transferSize – size of transfer in bytes (e.g.: 8, 4k, 2m, 1g)

> size (in bytes) of a single data buffer to be transferred in a single I/O call (default: 262144)

$$
\mathrm{aggregate\ filesize} = numTasks \times segmentCount \times blockSize
$$

假设有4个客户端节点，总共运行64个任务，segmentCount设置为16，blockSize设置为16MB，transferSize设置为1m。那么，每个节点需要执行4个任务，每个任务包含16个块的读写操作。每个任务一次读写1MB的数据，因此，一个块需要执行16次读写操作。总的读写数据大小为：
$$
\mathrm{aggregate\ filesize}=64 \times 16 \times 16MB = 16GB
$$

### 测试示例

1.shared file: All processes access a single file, 默认情况下使用该访问模式。

2.file-per-process: Each process is accessing its own file，需要添加`-F`选项。

3.顺序访问模式和随机访问模式（sequential  and random access pattern）

| Access Pattern \ Contention | shared file | file-per-process |
| :-------------------------: | :---------: | :--------------: |
|         sequential          |             |                  |
|           random            |             |                  |

> 16 processes ran on each client with each process writing and reading 4 GiB in total.

1.shared file + sequential access pattern:

```shell
mpirun -n 16 ./ior -t 1m -b 16m -s 256
```

2.shared file + random access pattern

```shell
mpirun -n 16 ./ior -t 1m -b 16m -s 256 -F
```

3.file-per-process: 

```shell
mpirun -n 16 ./ior -t 1m -b 16m -s 256 -z
```

### 参考资料

1.https://ior.readthedocs.io/en/latest/

2.GekkoFS — A Temporary Burst Buffer File System for HPC Applications. CLUSTER'18
