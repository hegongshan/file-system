Filebench是一种文件系统和存储测试工具，可以生成大量的工作负载。

### Filebench介绍

2002年，SUN公司开发了Filebench，并于2005年公开了源代码。

在SUN公司停止开发后，石溪大学的文件系统和存储实验室获得了许可，由他们负责继续开发和维护<sup>[[1]](#ref-1)</sup>。

根据谷歌学术的统计结果，有超过500篇论文使用Filebench作为测试工具<sup>[[2]](#ref-2)</sup>。

### 安装Filebench

#### 直接安装

```bash
yum install -y filebench
```

#### 手动安装

1.安装依赖

```bash
yum install -y flex bison
```

2.从[官网](https://github.com/filebench/filebench/releases)选择需要的版本，最新的版本为`v1.5-alpha3`：

```bash
wget https://github.com/filebench/filebench/releases/download/1.5-alpha3/filebench-1.5-alpha3.tar.gz
```

3.解压

```bash
tar -zxf filebench-1.5-alpha3.tar.gz
cd filebench-1.5-alpha3
```

4.配置、编译以及安装

```bash
./configure
make
make install
```

### 使用Filebench

Filebench定义了一门用于描述工作负载的语言——WML（Workload Model Language），并预先定义好了一些工作负载。

默认情况下，这些工作负载配置文件位于：`/usr/local/share/filebench/workloads`。

<img src="/img/file-system-benchmark-filebench-workload.png" alt="工作负载" width="300px" height="300px"/>

在测试时，官方不建议直接使用这些配置文件。如果需要使用这些工作负载，可以先复制一份，再进行修改：

```bash
cp /usr/local/share/filebench/workloads/webserver.f mywebserver.f
```

最后，运行工作负载：

```bash
filebench -f mywebserver.f
```

输出的部分结果如下所示：

![工作负载](/img/file-system-benchmark-filebench-webserver-result.png)

### 测试结果说明

Filebench的测试结果在wiki中有较为详细的说明<sup>[[3]](#ref-3)</sup>，对应的代码位于`stats.c`中。

1.每个操作的输出代码如下所示：

```c
while (flowop) {
  // ...
  (void) snprintf(line, sizeof(line), "%-20s %dops %8.0lfops/s "
                  "%5.1lfmb/s %8.3fms/op",
                  flowop->fo_name,
                  flowop->fo_stats.fs_count, 
                  flowop->fo_stats.fs_count / total_time_sec,
                  (flowop->fo_stats.fs_bytes / MB_FLOAT) / total_time_sec,
                  flowop->fo_stats.fs_count ?
                  flowop->fo_stats.fs_total_lat /
                  (flowop->fo_stats.fs_count * SEC2MS_FLOAT) : 0);
  (void) strcat(str, line);

  (void) snprintf(line, sizeof(line)," [%.3fms - %5.3fms]",
                  flowop->fo_stats.fs_minlat / SEC2MS_FLOAT,
                  flowop->fo_stats.fs_maxlat / SEC2MS_FLOAT);
  (void) strcat(str, line);
  // ...
}

// filebench.h
#define	KB_FLOAT ((double)1024.0)
#define	MB_FLOAT (KB_FLOAT * KB_FLOAT)

// fbtime.h
#define	SEC2MS_FLOAT (double)1000000.0
```

以`readfile1`操作为例，

```c
readfile1            9787ops      163ops/s  10.0mb/s      0.0ms/op [0.00ms -  0.88ms]
```

上述结果表明：

* 本次测试共执行了9787次读取操作
* 每秒执行的平均读取次数为163次
* 每秒读取的平均数据10MB
* 每次操作的平均延迟为0.0ms，每次操作的最小延迟为0.00ms，最大延迟为0.88ms

2.总的统计代码如下所示：

```c
filebench_log(LOG_INFO,
	    "IO Summary: %5d ops %5.3lf ops/s %0.0lf/%0.0lf rd/wr "
	    "%5.1lfmb/s %5.3fms/op",
	    iostat->fs_count + aiostat->fs_count,
	    (iostat->fs_count + aiostat->fs_count) / total_time_sec,
	    (iostat->fs_rcount + aiostat->fs_rcount) / total_time_sec,
	    (iostat->fs_wcount + aiostat->fs_wcount) / total_time_sec,
	    ((iostat->fs_bytes + aiostat->fs_bytes) / MB_FLOAT)
						/ total_time_sec,
	    (iostat->fs_count + aiostat->fs_count) ?
	    (iostat->fs_total_lat + aiostat->fs_total_lat) /
	    ((iostat->fs_count + aiostat->fs_count) * SEC2MS_FLOAT) : 0);
```

依旧以上面的测试结果为例：

```c
IO Summary: 303389 ops 5055.849 ops/s 1631/163 rd/wr 100.7mb/s   0.0ms/op
```

其表明：

* 本次测试共执行了303389次各类操作
* 每秒执行的平均操作次数为5055.849
* 每秒执行的平均读操作次数为1631，平均写操作次数为163次
* 每秒读写的平均数据为100.7MB
* 每次操作的平均延迟为0.0ms

### 参考资料

<p id="ref-1">[1] Filebench，https://github.com/filebench/filebench</p>

<p id="ref-2">[2] Filebench: A Flexible Framework for File System Benchmarking, ;login: The USENIX Magazine'16, https://www.usenix.org/system/files/login/articles/login_spring16_02_tarasov.pdf</p>

<p id="ref-3">[3] Collected metrics，https://github.com/filebench/filebench/wiki/Collected-metrics</p>
