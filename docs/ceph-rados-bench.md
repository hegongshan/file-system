Ceph提供了RADOS测试工具：

```bash
rados [-c conf] [-k keyring] -p <pool> bench <seconds> write|seq|rand 
[-t concurrent_operations] [--run-name run_name] [--show-time] 
[-b block_size] [-O object_size] [--max-objects num] [--no-cleanup] [--reuse-bench] 
[--write-object] [--write-omap] [--write-xattr] 
[--no-verify]
```

### 参数说明

Ceph相关选项：

* `-c conf`：设置Ceph的配置文件，文件指定了集群中mon的地址，默认为`/etc/ceph/ceph.conf`。
* `-k keyring`：设置秘钥环。默认从`/etc/ceph/`目录下读取，例如，`/etc/ceph/ceph.client.admin.keyring`。

必要的选项：

* `-p pool_name`：设置测试使用的存储池；
* `<seconds>`：设置测试的运行时间，在读测试中，如果数据已经全部读完，测试将会提前结束；
* `write|seq|rand`：write表示写操作，seq表示顺序读，rand表示随机读；

可选的选项：

* `-t concurrent_operations`：设置并发IO操作的数量，默认为16；
* `--run-name run_name`：设置测试的名称，默认为`benchmark_last_metadata`。在写测试中，用于标识当前的测试；在读测试中，用于指定需要读取的数据。
* `--show-time`：在输出的内容前添加时间信息。

写操作特有的选项：

* `-b block_size`：设置块大小，默认为4 MB。若对象大小<块大小，则块大小被设置为对象大小；
* `-O object_size`：设置对象大小。如果没有设置，则等于块大小；

如果在读操作中设置了以上两个选项，将产生如下错误：

```bash
-b|--block_size option can be used only with 'write' bench test
```

* `--max-objects num`: 设置写操作的最大对象数；
* `--no-cleanup`：写完以后，不要删除测试数据。默认情况下，写完数据后，会自动删除测试数据。
* `--reuse-bench`：重用上一次的写bench。关于该选项的说明，参见[一些疑问](#一些疑问)。

如果在读操作中设置了以上三个选项，将会被忽略。

* `--write-object`：写到对象中。默认情况下，rados会写对象。
* `--write-omap`：写到omap中。
* `--write-xattr`：写到扩展属性中。

如果在读操作中设置了以上三个选项，将产生如下错误：

```bash
--write-object, --write-omap and --write-xattr options can only be used with the 'write' bench test
```

读操作特有的选项：

`--no-verify`：不验证读取的内容。如果没有设置该选项，读测试将会比较当前读取的内容与之前写入的内容是否一致。

### 结果说明

在测试的过程中，每秒都会输出如下格式的实时数据：

|     sec      |       Cur ops        |           started            |           finished           |         avg MB/s         |    cur MB/s    |       last lat(s)        |        avg lat(s)        |
| :----------: | :------------------: | :--------------------------: | :--------------------------: | :----------------------: | :------------: | :----------------------: | :----------------------: |
| 当前是第几秒 | 正在执行的并发IO数量 | 累计至当前时刻已发出的请求数 | 累计至当前时刻已完成的请求数 | 累计至当前时刻的平均带宽 | 当前秒内的带宽 | 当前秒内最后一次IO的延迟 | 累计至当前时刻的平均延迟 |

测试结束后，radosbench会计算三类指标：带宽、IOPS、延迟。

对于每类指标，radosbench会输出四个值，分别为平均值、标准差、最大值、最小值。

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

### 动手实践

#### 创建存储池

```bash
ceph osd pool create test-rados 256 256
```

#### 清除缓存

每次执行测试前，都应该先清除缓存

```bash
sudo sync && sudo echo 3 > /proc/sys/vm/drop_caches
```

#### 写测试

```bash
[root@hgs ~]# rados -p test-rados bench 10 write -b 8M -t 48 --run-name test --no-cleanup 
hints = 1
Maintaining 48 concurrent writes of 8388608 bytes to objects of size 8388608 for up to 10 seconds or 0 objects
Object prefix: benchmark_data_hgs_32707
  sec Cur ops   started  finished  avg MB/s  cur MB/s last lat(s)  avg lat(s)
    0       0         0         0         0         0           -           0
    1      47        51         4   31.9983        32    0.998271     0.97599
    2      47       106        59   235.976       440    0.805757    0.997677
    3      47       150       103   274.634       352     1.00034    0.997778
    4      47       199       152   303.963       392    0.828574    0.992927
    5      47       251       204   326.357       416    0.868755    0.960068
    6      47       294       247    329.29       344     1.11908    0.967595
    7      47       344       297   339.384       400    0.945639    0.988173
    8      47       392       345   344.955       384    0.972711    0.988318
    9      47       443       396   351.952       408    0.924125    0.985778
   10      47       490       443   354.351       376    0.993385    0.987729
Total time run:         10.284
Total writes made:      491
Write size:             8388608
Object size:            8388608
Bandwidth (MB/sec):     381.954
Stddev Bandwidth:       116.851
Max bandwidth (MB/sec): 440
Min bandwidth (MB/sec): 32
Average IOPS:           47
Stddev IOPS:            14.6063
Max IOPS:               55
Min IOPS:               4
Average Latency(s):     0.946436
Stddev Latency(s):      0.251772
Max latency(s):         3.81698
Min latency(s):         0.144889
```

#### 读测试

注意：必须先写入数据，然后才能读取数据。如果使用相同的`run_name`执行了多次写操作，则只会读取最后一次写入的内容。

```bash
[root@hgs ~]# rados -p test-rados bench 10 seq -t 48 --run-name test
hints = 1
  sec Cur ops   started  finished  avg MB/s  cur MB/s last lat(s)  avg lat(s)
    0       0         0         0         0         0           -           0
    1      47       101        54   431.109       432    0.510649    0.493482
    2      47       200       153   611.329       792    0.458691    0.488479
    3      47       282       235   624.416       656    0.537287    0.496105
    4      47       366       319   636.249       672    0.524269     0.51689
    5      47       454       407   649.741       704    0.512594    0.534953
Total time run:       5.5275
Total reads made:     491
Read size:            8388608
Object size:          8388608
Bandwidth (MB/sec):   710.629
Average IOPS:         88
Stddev IOPS:          16.6673
Max IOPS:             99
Min IOPS:             54
Average Latency(s):   0.50245
Max latency(s):       3.62436
Min latency(s):       0.0784325
```

顺序读只花费了5.5s就读完了全部的数据。

#### 删除测试数据

```bash
rados -p <pool> cleanup [--run-name run_name] [--prefix prefix]
```

* `--prefix`：设置需要删除的对象名前缀，默认为`benchmark_data_{hostname}`。

1.删除`run_name`中的所有数据：

```bash
rados -p test-rados cleanup --run-name test-rados
```

2.删除存储池

```bash
ceph osd pool rm test-rados test-rados --yes-i-really-really-mean-it
```

### 一些疑问

* **问题1：对于读操作，为什么指定`--run_name`后就能读取到之前写入的数据？**

答：写测试结束后，radosbench会创建一个名为`run_name`的对象，用于记录**本次测试**的元数据（每执行一次写测试，对象中存储的数据就会更新一次）。

该对象依次记录了测试设置的对象大小（long型，占8个字节）、执行的写入次数（int型，占4个字节）、测试进程的PID（int型，占4个字节）以及块大小（long型，占8个字节），如下所示（代码位于`src/common/obj_bencher.cc/ObjBencher::write_bench`中）：

```c
//write object size/number data for read benchmarks
encode(data.object_size, b_write);
encode(data.finished, b_write);
encode(prev_pid ? prev_pid : getpid(),  b_write);
encode(data.op_size, b_write);

// persist meta-data for further cleanup or read
sync_write(run_name_meta, b_write, sizeof(int)*3);
```

在测试时，创建的对象则采用`benchmark_data_{hostname}_{pid}_object{objnum}`作为命名格式。

对于读操作，只需要读取`run_name`对象中存储的数据，就可以得到上述信息，进而读取上一次写入的数据。为了验证问题1，首先执行一个写测试，将块大小设置为16M：

```bash
[root@hgs ~]# rados -p test-rados bench 10 write -b 16M --run-name test --no-cleanup 
hints = 1
Maintaining 16 concurrent writes of 16777216 bytes to objects of size 16777216 for up to 10 seconds or 0 objects
Object prefix: benchmark_data_hgs_19723
...
Total writes made:      186
...
```

然后，查看本次测试创建的对象：

```bash
[root@hgs ~]# rados -p test-rados ls | grep test
test
[root@hgs ~]# rados -p test-rados ls | grep benchmark_data_hgs_19723_object | wc -l
186
```

输出结果表明：radosbench确实创建了名为`test`的对象和指定名称的186个对象。

接着，使用如下命令导出test对象中存储的数据：

```bash
rados -p test-rados get test test-data.txt
```

导出的结果是二进制的，为了便于分析，下面将其转换成十六进制：

```bash
[root@hgs ~]# hexdump test-data.txt
0000000 0000 0100 0000 0000 00ba 0000 4d0b 0000
0000010 0000 0100 0000 0000                    
0000018
```

笔者的机器采用的字节顺序为Little Endian，即低位字节存放在低地址中，高位字节存放在高地址中。按照该字节序将输出的十六进制数字转换为对应的十进制：
$$
\begin{aligned}
0x0000\ 0000\ 0100\ 0000 &= 16777216B = 16M \\
0x0000\ 00ba &= 186 \\
0x0000\ 4d0b &= 19723
\end{aligned}
$$
这与设置的对象大小（块大小）、执行的写入次数以及测试进程的PID是一致的。



* **问题2：对于写操作，添加选项`--reuse-bench`，重用的东西到底是什么？**

答：添加选项`--reuse-bench`，表明使用`run_name`对象中存储的块大小（对象大小）作为测试的块大小（对象大小），使用存储的进程PID格式化写入对象的名称。此时，`-b`和`-O`选项将会失效，如下所示（代码位于`src/common/obj_bencher.cc/ObjBencher::aio_bench`）：

```bash
//get data from previous write run, if available
if (operation != OP_WRITE || reuse_bench) {
    uint64_t prev_op_size, prev_object_size;
    r = fetch_bench_metadata(run_name_meta, &prev_op_size, &prev_object_size,
			     &num_ops, &num_objects, &prev_pid);
    if (r < 0) {
      if (r == -ENOENT) {
        if (reuse_bench)
          cerr << "Must write data before using reuse_bench for a write benchmark!" << std::endl;
        else
          cerr << "Must write data before running a read benchmark!" << std::endl;
      }
      return r;
    }
    object_size = prev_object_size;   
    op_size = prev_op_size;           
}
```

* **问题3：在设置访问模式时，对于读测试，可以指定采用顺序读还是随机读，而写测试却只有一个模式write，它到底是顺序写还是随机写呢？**

答：与文件不同，对象作为一个整体，其内部只支持顺序访问。

1.当执行顺序读（seq）时，radosbench会按照对象名从小到大的顺序依次读取所有对象。是否读完所有对象和运行时间是否结束，二者均可以作为终止条件；

2.当执行随机读（rand）时，radosbench会随机选择要读取的对象（可能会重复读取同一个对象），直到运行时间结束。

3.当执行写（write）操作时，radosbench会按照对象名从小到大的顺序依次写入。

* **问题4：当使用多个客户端进行测试时，如果客户端A先执行写测试，然后客户端B再去读取A写入的数据，B无法从存储池中正确读取数据。**

对象采用`benchmark_data_{hostname}_{pid}_object{objnum}`的命名格式，不同客户端的主机名并不相同。

当客户端A写入对象时，对象名中的hostname使用的是A的主机名；

当客户端B读取对象时，它将使用自己的hostname格式化对象名，从而导致读取失败。

参考资料

[1] https://github.com/ceph/ceph/blob/master/src/tools/rados/rados.cc

[2] https://github.com/ceph/ceph/blob/master/src/common/obj_bencher.cc
