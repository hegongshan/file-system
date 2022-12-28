### HDFS基准测试

#### Benchmark

```bash
$ hadoop jar ${HADOOP_HOME}/share/hadoop/mapreduce/hadoop-mapreduce-client-jobclient-*-tests.jar
```

问题：执行上述命令后，可能会出现找不到junit的TestCase类：

```bash
java.lang.NoClassDefFoundError: junit/framework/TestCase
Caused by: java.lang.ClassNotFoundException: junit.framework.TestCase
```

解决办法：将`junit-*.jar`复制到目录`${HADOOP_HOME}/share/hadoop/common/lib/`中。例如：

```bash
cp ${HADOOP_HOME}/share/hadoop/tools/lib/junit-*.jar ${HADOOP_HOME}/share/hadoop/common/lib/
```

#### TestDFSIO

TestDFSIO测试包含的选项如下所示：

```bash
Usage: TestDFSIO [genericOptions] -read [-random | -backward | -skip [-skipSize Size]] | -write | -append | -truncate | -clean [-compression codecClassName] [-nrFiles N] [-size Size[B|KB|MB|GB|TB]] [-resFile resultFileName] [-bufferSize Bytes] [-storagePolicy storagePolicyName] [-erasureCodePolicy erasureCodePolicyName]
```

写测试：

```bash
$ hadoop jar ${HADOOP_HOME}/share/hadoop/mapreduce/hadoop-mapreduce-client-jobclient-*-tests.jar TestDFSIO -write -nrFiles 10 -size 128MB -resFile result
```

读测试

```bash
# 清空缓存
$ sync && echo 3 > /proc/sys/vm/dop_cache
$ hadoop jar ${HADOOP_HOME}/share/hadoop/mapreduce/hadoop-mapreduce-client-jobclient-*-tests.jar TestDFSIO -read
```

输出结果如下所示：

```bash
2022-12-28 14:36:36,650 INFO fs.TestDFSIO: ----- TestDFSIO ----- : write
2022-12-28 14:36:36,650 INFO fs.TestDFSIO:             Date & time: Wed Dec 28 14:36:36 CST 2022
2022-12-28 14:36:36,651 INFO fs.TestDFSIO:         Number of files: 10
2022-12-28 14:36:36,651 INFO fs.TestDFSIO:  Total MBytes processed: 1280
2022-12-28 14:36:36,651 INFO fs.TestDFSIO:       Throughput mb/sec: 7.4
2022-12-28 14:36:36,651 INFO fs.TestDFSIO:  Average IO rate mb/sec: 15.18
2022-12-28 14:36:36,651 INFO fs.TestDFSIO:   IO rate std deviation: 11.52
2022-12-28 14:36:36,651 INFO fs.TestDFSIO:      Test exec time sec: 109
```
其中，Average IO rate表示每个Mapper的平均速度，Throughput表示测试的总吞吐率。它们的计算过程如下所示：

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

删除测试数据

```bash
$ hadoop jar ${HADOOP_HOME}/share/hadoop/mapreduce/hadoop-mapreduce-client-jobclient-*-tests.jar TestDFSIO -clean
```

### 参考文献

1. Distributed i/o benchmark, https://github.com/apache/hadoop/blob/trunk/hadoop-mapreduce-project/hadoop-mapreduce-client/hadoop-mapreduce-client-jobclient/src/test/java/org/apache/hadoop/fs/TestDFSIO.java
