1.查看文件系统的名称，通常为`hdfs://<namenode>:9000`：

```bash
$ hdfs getconf -confKey fs.defaultFS
hdfs://localhost:9000
```

2.临时文件的存放路径`hadoop.tmp.dir`，默认为`/tmp/hadoop-{user.name}`

```bash
$ hdfs getconf -confKey hadoop.tmp.dir
/tmp/hadoop-root
```

3.namenode数据的存放位置

```bash
$ hdfs getconf -confKey dfs.namenode.name.dir
file:///tmp/hadoop-root/dfs/name
```

`dfs.namenode.name.dir`的目录结构如下所示：

```bash
$ tree /tmp/hadoop-root/dfs/name
/tmp/hadoop-root/dfs/name
├── current
│   ├── edits_0000000000000000001-0000000000000000003
│   ├── edits_inprogress_0000000000000002751
│   ├── fsimage_0000000000000002748
│   ├── fsimage_0000000000000002748.md5
│   ├── fsimage_0000000000000002750
│   ├── fsimage_0000000000000002750.md5
│   ├── seen_txid
│   └── VERSION
└── in_use.lock
```

4.datanode数据的存放位置

```bash
$ hdfs getconf -confKey dfs.datanode.data.dir
file:///tmp/hadoop-root/dfs/data
```

`dfs.datanode.data.dir`的目录结构如下所示：

```bash
$ tree /tmp/hadoop-root/dfs/data
/tmp/hadoop-root/dfs/data
├── current
│   ├── BP-421604034-172.16.2.24-1668848846395
│   │   ├── current
│   │   │   ├── dfsUsed
│   │   │   ├── finalized
│   │   │   │       └── subdir1
│   │   │   │           ├── blk_1073742151
│   │   │   │           ├── blk_1073742151_1327.meta
│   │   │   │           ├── blk_1073742152
│   │   │   │           └── blk_1073742152_1328.meta
│   │   │   ├── rbw
│   │   │   └── VERSION
│   │   ├── scanner.cursor
│   │   └── tmp
│   └── VERSION
└── in_use.lock
```

5.查看副本数

```bash
$ hdfs getconf -confKey dfs.replication
1
```

6.查看块大小

```bash
$ hdfs getconf -confKey dfs.blocksize
134217728
```

输出的值以字节为单位，134217728 / 1024 / 1024 = 128MB