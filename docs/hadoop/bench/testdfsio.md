TestDFSIO测试包含的选项如下所示：

```bash
Usage: TestDFSIO [genericOptions] -read [-random | -backward | -skip [-skipSize Size]] | -write | -append | -truncate | -clean [-compression codecClassName] [-nrFiles N] [-size Size[B|KB|MB|GB|TB]] [-resFile resultFileName] [-bufferSize Bytes] [-storagePolicy storagePolicyName] [-erasureCodePolicy erasureCodePolicyName]
```

测试的根目录默认为`/benchmarks/TestDFSIO`，该值可以通过配置`test.build.data`指定。

### 选项说明

测试类型：

* `-read`：读测试，默认为顺序读

1. `-random`：随机读，随机选择一个位置进行读取

2. `-backward`：反向读，反向读取文件

3. `-skip [-skipSize Size]`：跳跃读，每次读取之后，跳过特定字节

* `-write`：写测试

* `-append`：追加测试

* `-truncate`：截断测试

* `-clean`：清除测试生成的所有文件

测试选项：

* `-nrFiles N`：指定需要读写的文件数量，默认为1个。

* `-size Size[B|KB|MB|GB|TB]`：指定每个文件的大小，默认为1MB。

* `-resFile`：指定本地存放测试结果的文件名，默认为`TestDFSIO_results.log`

* `-bufferSize Bytes`：指定读写缓冲区的大小，默认为1000000字节。

### 执行测试

* 写测试：

首先，执行写测试：

```bash
$ hadoop jar ${HADOOP_HOME}/share/hadoop/mapreduce/hadoop-mapreduce-client-jobclient-*-tests.jar TestDFSIO -write -nrFiles 10 -size 128MB
```

部分输出结果如下所示：

```bash
2022-12-28 16:23:19,917 INFO fs.TestDFSIO: ----- TestDFSIO ----- : write
2022-12-28 16:23:19,940 INFO fs.TestDFSIO:             Date & time: Wed Dec 28 16:23:19 CST 2022
2022-12-28 16:23:19,940 INFO fs.TestDFSIO:         Number of files: 10
2022-12-28 16:23:19,940 INFO fs.TestDFSIO:  Total MBytes processed: 1280
2022-12-28 16:23:19,940 INFO fs.TestDFSIO:       Throughput mb/sec: 117.47
2022-12-28 16:23:19,940 INFO fs.TestDFSIO:  Average IO rate mb/sec: 117.66
2022-12-28 16:23:19,941 INFO fs.TestDFSIO:   IO rate std deviation: 4.77
2022-12-28 16:23:19,941 INFO fs.TestDFSIO:      Test exec time sec: 21.85
```
其中，`Throughput`表示测试的总吞吐率（mb/sec），`Average IO rate`表示每个map任务的平均I/O速度（mb/sec），`IO rate std deviation`表示I/O速度的标准差，`Test exec time`表示程序执行的总时间。

上述评价指标的计算过程如下所示：
$$
\begin{aligned}
\mathrm{rate_i} &= \frac{size_i}{time_i}\\
\mathrm{Throughput} &= \frac{\sum_i^N size_i}{\sum_i^N time_i}\\
\mathrm{Average\ IO\ rate} &= \frac{\sum_i^N rate_i}{N}\\
\mathrm{Deviation} &= \frac{\sum_{i=1}^N (x_i - \mu)^2}{N}\\
&= \frac{\sum_{i=1}^N x_i^2}{N} - 2 \mu \frac{\sum_{i=1}^N x_i}{N} + \frac{\sum_{i=1}^N \mu^2}{N}\\
&= \frac{\sum_{i=1}^N x_i^2}{N} - 2 \mu^2 + \mu^2\\
&= \frac{\sum_{i=1}^N x_i^2}{N} - \mu^2\\
stddev &= \sqrt{Deviation}
\end{aligned}
$$

在TestDFSIO中，相应的计算代码如下所示：

```java
// 对于每个Mapper，计算rate_i
float ioRateMbSec = (float)totalSize * 1000 / (execTime * MEGA);

// 计算总的吞吐率、IO rate以及Io rate stddev 
toMB(size) / msToSecs(time)
double med = rate / 1000 / tasks;
double stdDev = Math.sqrt(Math.abs(sqrate / 1000 / tasks - med*med));
```

接下来，我们验证下输出结果。首先，查看输出文件：


```bash
$ hdfs dfs -cat /benchmarks/TestDFSIO/io_write/*
f:rate	1176577.1
f:sqrate	1.38661264E8
l:size	1342177280
l:tasks	10
l:time	10896
```

其中，time表示写操作执行的总时间。

然后，手动计算三个指标的值：
$$
\begin{aligned}
\mathrm{Throughput} &= \frac{1280}{10.896} \approx 117.47 \\
\mathrm{Average\ IO\ rate} &= \frac{1176577.1}{1000 * 10} \approx 117.66\\
\mathrm{IO\ rate\ std\ deviation} &=\sqrt{\frac{1.38661264E8}{1000 * 10} - \left(\frac{1176577.1}{1000 * 10}\right)^2} \approx 4.77
\end{aligned}
$$
可以发现，手动计算的结果与输出结果是一致的。

* 读测试

```bash
# 清空缓存
$ sync && echo 3 > /proc/sys/vm/drop_caches 
$ hadoop jar ${HADOOP_HOME}/share/hadoop/mapreduce/hadoop-mapreduce-client-jobclient-*-tests.jar TestDFSIO -read -nrFiles 10 -size 128MB
```

输出结果如下所示：

```bash
2022-12-28 16:33:33,332 INFO fs.TestDFSIO: ----- TestDFSIO ----- : read
2022-12-28 16:33:33,372 INFO fs.TestDFSIO:             Date & time: Wed Dec 28 16:33:33 CST 2022
2022-12-28 16:33:33,372 INFO fs.TestDFSIO:         Number of files: 10
2022-12-28 16:33:33,372 INFO fs.TestDFSIO:  Total MBytes processed: 1280
2022-12-28 16:33:33,372 INFO fs.TestDFSIO:       Throughput mb/sec: 10.5
2022-12-28 16:33:33,372 INFO fs.TestDFSIO:  Average IO rate mb/sec: 10.54
2022-12-28 16:33:33,372 INFO fs.TestDFSIO:   IO rate std deviation: 0.59
2022-12-28 16:33:33,372 INFO fs.TestDFSIO:      Test exec time sec: 31.05
```

查看输出文件：

```bash
$ hdfs dfs -cat /benchmarks/TestDFSIO/io_read/*
f:rate	105363.32
f:sqrate	1113640.0
l:size	1342177280
l:tasks	10
l:time	121853
```

* 删除测试数据

首先，参看生成的文件：

```bash
$ hdfs dfs -ls /benchmarks/TestDFSIO
Found 4 items
drwxr-xr-x   - root supergroup          0 2022-12-28 16:33 /benchmarks/TestDFSIO/io_control
drwxr-xr-x   - root supergroup          0 2022-12-28 16:23 /benchmarks/TestDFSIO/io_data
drwxr-xr-x   - root supergroup          0 2022-12-28 16:33 /benchmarks/TestDFSIO/io_read
drwxr-xr-x   - root supergroup          0 2022-12-28 16:23 /benchmarks/TestDFSIO/io_write
```

然后，执行删除操作：

```bash
$ hadoop jar ${HADOOP_HOME}/share/hadoop/mapreduce/hadoop-mapreduce-client-jobclient-*-tests.jar TestDFSIO -clean
2022-12-28 16:36:14,664 INFO fs.TestDFSIO: TestDFSIO.1.8
2022-12-28 16:36:14,665 INFO fs.TestDFSIO: nrFiles = 1
2022-12-28 16:36:14,666 INFO fs.TestDFSIO: nrBytes (MB) = 1.0
2022-12-28 16:36:14,666 INFO fs.TestDFSIO: bufferSize = 1000000
2022-12-28 16:36:14,666 INFO fs.TestDFSIO: baseDir = /benchmarks/TestDFSIO
2022-12-28 16:36:15,555 INFO fs.TestDFSIO: Cleaning up test files
```

最后，检查测试文件是否已经删除：

```bash
$ hdfs dfs -ls /benchmarks/TestDFSIO
ls: `/benchmarks/TestDFSIO': No such file or directory
```

### 参考文献

1. Distributed i/o benchmark, https://github.com/apache/hadoop/blob/trunk/hadoop-mapreduce-project/hadoop-mapreduce-client/hadoop-mapreduce-client-jobclient/src/test/java/org/apache/hadoop/fs/TestDFSIO.java
