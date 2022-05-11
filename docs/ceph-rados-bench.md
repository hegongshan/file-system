## Ceph RADOS测试工具

### 测试工具

Ceph自带了一个RADOS的测试工具：

```bash
rados bench -p <pool> [-b block_size] [-O object_size] [--max-objects num]
<seconds> write|seq|rand 
[-t concurrent_operations] [--no-cleanup] [--run-name run_name] [--no-hints] [--reuse-bench]
```

参数说明：

* `-p`：设置测试使用的存储池；
* `-b`：设置块大小，默认为4 MB。若对象大小<块大小，则块大小被设置为对象大小；
* `-O`：设置对象大小。如果没有设置，则等于块大小；
* `--max-objects`: 设置写操作的最大对象数；
* `write|seq|rand`：write表示写操作，seq表示顺序读，rand表示随机读；
* `-t`：设置并发IO数，默认为16；
* `--no-cleanup`：写完以后，不要删除测试数据。默认情况下，写完数据后，会自动删除测试数据。
* `--run-name`：设置测试的名称，默认为`benchmark_last_metadata`。用于删除指定测试的数据。

### 测试结果

测试过程中的输出为：

|  sec   | Cur ops | started | finished                    | avg MB/s                | cur MB/s            | last lat(s)         | avg lat(s)           |
| :----: | :-----: | :-------: | :---------------------------: | :-----------------------: | :-------------------: | :-------------------: | :--------------------: |
| 当前是第几秒 | 并发IO数 | 累计至当前时刻已发出的请求数 | 累计至当前时刻已完成的请求数 | 累计至当前时刻的平均带宽 | 当前时刻的带宽 | 当前秒内的延迟 | 累计至当前时刻的平均延迟 |



rados bench会计算三类指标：带宽、IOPS、延迟。

对于每类指标，rados bench会输出四个值，分别为平均值、标准差、最大值、最小值。

以平均值为例，计算方法如下所示：


$$
\mathrm{Bandwidth = \frac{finished \times block\ size}{timePassed}} \tag{1}
$$

$$
\mathrm{Average\ IOPS = \frac{finished}{timePassed}} \tag{2}
$$

$$
\mathrm{Average\ Latency = \frac{Total\ Latency}{finished}}\tag{3}
$$

### 示例输出

```bash
[root@hgs ~]# rados -p test-rados -b 8M bench 10 write -t 48 --no-cleanup --run-name=test
hints = 1
Maintaining 48 concurrent writes of 8388608 bytes to objects of size 8388608 for up to 10 seconds or 0 objects
Object prefix: benchmark_data_xdata1_30219
  sec Cur ops   started  finished  avg MB/s  cur MB/s last lat(s)  avg lat(s)
    0       0         0         0         0         0           -           0
    1      47        52         5   39.9971        40    0.962927    0.956875
    2      47        99        52   207.975       376     1.00051    0.995987
    3      47       154       107   285.291       440    0.912885    0.972533
    4      47       194       147   293.957       320     0.98964    0.985321
    5      47       242       195   311.953       384      1.0869     1.00309
    6      47       293       246   327.951       408    0.881876    0.991712
    7      47       343       296   338.235       400    0.903296    0.985563
    8      47       395       348   347.945       416     0.97344     0.97706
    9      47       444       397   352.833       392    0.922008    0.977128
   10      47       496       449   359.144       416    0.944612    0.972914
Total time run:         10.2284
Total writes made:      497
Write size:             8388608
Object size:            8388608
Bandwidth (MB/sec):     388.72
Stddev Bandwidth:       116.631
Max bandwidth (MB/sec): 440
Min bandwidth (MB/sec): 40
Average IOPS:           48
Stddev IOPS:            14.5789
Max IOPS:               55
Min IOPS:               5
Average Latency(s):     0.931488
Stddev Latency(s):      0.160404
Max latency(s):         1.17402
Min latency(s):         0.126361
```



参考资料

[1] https://github.com/ceph/ceph/blob/master/src/tools/rados/rados.cc

[2] https://github.com/ceph/ceph/blob/master/src/common/obj_bencher.cc
