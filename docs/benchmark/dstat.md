dstat

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