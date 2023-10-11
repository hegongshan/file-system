fio是一个常用的I/O测试工具，基于不同的测试引擎可以产生多种类型的测试压力。其作者Jens Axboe是Linux内核的维护者之一，主要负责内核中的块设备部分，fio原本是他为了模拟读/写负载能力而开发的。

<!--more-->

### 安装fio

1.从[GitHub](https://github.com/axboe/fio)中下载需要的版本，以`3.28`版本为例：

```shell
wget https://github.com/axboe/fio/archive/refs/tags/fio-3.28.zip
```

2.解压缩

```shell
unzip fio-3.28.zip
cd fio-fio-3.28/
```

3.执行配置程序

```shell
./configure
```

4.编译

```shell
make -j5
```

5.安装

```shell
make install
```

6.查看安装的位置和版本

```shell
[root@hgs fio-fio-3.28]# which fio
/usr/local/bin/fio
[root@hgs fio-fio-3.28]# fio -v
fio-3.28
```

### 使用fio

fio既可以使用命令行参数执行，也可以通过指定jobfile执行。

```shell
$ fio [options] [jobfile] ...
```

#### 任务文件的格式

参考[Job file format](https://fio.readthedocs.io/en/latest/fio_doc.html#job-file-format)

```shell
[job name]
parameters
```

常见的任务参数：

* directory：设置目录
* filename：设置需要测试的磁盘名或文件名，多个磁盘或文件名通过冒号`:`分隔，例如`/dev/sda:/dev/sdb`；如果路径名中含有`:`，则需要进行转义，例如路径名为`F:\filename`，则`filename=F\:\filename`
* ioengine：设置使用的IO引擎

1.libaio：使用Linux中的异步读写接口

```shell
yum install -y libaio-devel
```

安装libaio以后，需要重新执行配置、编译以及安装，否则会出现如下错误：

```shell
libaio: cannot open shared object file: No such file or directory
```

2.sync：同步读写

3.psync：原子同步读写

* `rw`和`readwrite`：设置读写模式

```shell
read: 顺序读
write: 顺序写
rw, readwrite: 混合顺序读写
randread: 随机读
randwrite: 随机写
randrw: 混合随机读写
```

* `direct=bool`：是否使用直接I/O，默认为false。
* `buffered=bool`：是否使用缓冲I/O，默认为true。
* `iodepth=int`：设置并行执行的I/O数量。

* `bs`和`blocksize`：设置块大小，默认的单位为字节，大小为4096。
* `runtime=time`：设置运行时间，默认的单位为秒。
* `ramp_time=time`：设置预热时间。
* `time_based`：即使文件已经读取或者写入完成，fio会继续执行测试，直到时间耗尽
* `size=int`：设置读写的大小
* `thread`：默认情况下，fio使用fork创建任务。如果设置了thread，则使用POSIX中的Thread创建任务。
* `group_reporting`：默认情况下，fio按照任务输出最终的测试结果。如果设置了`group_reporting`，则会按组输出最终的结果。
* `exec_prerun=str`：设置在测试开始前需要执行的命令
* `exec_postrun=str`：设置在测试完成后需要执行的命令
* `output=filename`：设置输出文件
* `loops=int`：重复执行指定的次数。默认为1次，即不重复执行。
* `numjobs=int`：设置任务数量，默认为1个任务。

#### 测试实例

运行fio，https://fio.readthedocs.io/en/latest/fio_doc.html#running-fio

> For example, for the job file parameter [`iodepth=2`](https://fio.readthedocs.io/en/latest/fio_man.html#cmdoption-arg-iodepth), the mirror command line option would be [`--iodepth 2`](https://fio.readthedocs.io/en/latest/fio_man.html#cmdoption-arg-iodepth) or [`--iodepth=2`](https://fio.readthedocs.io/en/latest/fio_man.html#cmdoption-arg-iodepth).

1.创建任务文件test.fio：

```shell
[global]
thread
direct=1
exec_prerun=echo 3 > /proc/sys/vm/drop_caches
ioengine=libaio
runtime=5
ramp_time=6
filename=/dev/sda
group_reporting

[randwrite]
description="Random Write"
rw=randwrite
size=1G
bs=4K
iodepth=16

[seqwrite]
description="Sequential Write"
rw=write
size=1G
bs=4M
iodepth=1
```

2.执行测试：

```shell
sudo fio test.fio --section=randwrite
```

### fio绘图

在执行测试时，添加如下参数：

```shell
write_bw_log=带宽日志前缀
write_iops_log=iops日志前缀
write_lat_log=latlat
log_avg_msec=1000
```

安装gnuplot绘图库。

```shell
yum install -y gnuplot
```

绘制图形：

```shell
fio2gnuplot -b -g
```

### 参考资料

1. https://github.com/axboe/fio
2. fio - Flexible I/O tester，https://fio.readthedocs.io
3. fio引擎libaio加载失败，https://support.huaweicloud.com/trouble-kunpengsdss/kunpengsdss_09_0012.html

