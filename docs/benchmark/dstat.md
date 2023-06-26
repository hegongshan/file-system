dstat是一个统计系统资源的通用工具。

### 说明

```bash
dstat [options..] [delay [count]]
```

和I/O相关的选项有：

* --aio: 统计aio信息

* --fs, --filesystem: 统计文件系统的信息

* --noupdate：不要输出中间结果

* --output file：以CSV格式，将结果输出到file中

delay：每次更新的间隔时间（单位为秒），默认为1秒

count：输出多少次，默认为无限次

### 测试

```bash
$ dstat --fs --noupdate 2 2
--filesystem-
files  inodes
 4693    132k
 4693    132k
```

files和inodes的值来自于以下两个文件：

* /proc/sys/fs/file-nr

> https://www.kernel.org/doc/html/latest/admin-guide/sysctl/fs.html#file-max-file-nr

```bash
$ cat /proc/sys/fs/file-nr
4437	0	9223372036854775807
```

file-nr文件中的三个值分别表示：

1.已分配的文件句柄数

2.已分配但未使用的文件句柄数。该值始终为0，也就是说，已分配的文件句柄数与已使用的文件句柄数完全匹配。

3.文件句柄的最大数量

* /proc/sys/fs/inode-nr

> https://www.kernel.org/doc/html/latest/admin-guide/sysctl/fs.html#inode-max-inode-nr-inode-state

```bash
$ cat /proc/sys/fs/inode-nr
132971	471
```

`inode-nr`文件中的两个值分别为nr_inodes（即已分配的inode数）和nr_free_inodes（即空闲的inode数量）。
