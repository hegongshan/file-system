### HDFS Shell操作

### 创建操作

* mkdir：创建目录

```bash
$ hdfs dfs -mkdir input
```

* touch

### 读取操作

* ls：查看目录
* cat：输出文件内容

* head：输出文件的前1KB数据。

* tail：输出文件的最后1KB数据。

### 查看使用情况

* count：统计目录中包含的子目录数、文件数以及占用的字节数

```bash
$ hdfs dfs -count -h .
          13           21              2.0 G .
```

上面的输出说明：当前目录下包含13个子目录，21个文件，共占用2G的空间。

### 复制操作

* put：从本地文件系统拷贝文件到HDFS

* get：从HDFS中拷贝文件到本地文件系统
* cp：复制文件

### 删除操作

* rmdir：删除目录

```bash
$ hdfs dfs -rmdir input
```

* rm：删除文件

### 参考文献

1. https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/FileSystemShell.html

