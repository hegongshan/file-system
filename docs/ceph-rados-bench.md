### Ceph RADOS测试工具

Ceph自带了一个RADOS的测试工具：

```bash
rados bench <seconds> write|seq|rand 
[-t concurrent_operations] [--no-cleanup] [--run-name run_name] [--no-hints] [--reuse-bench]
```

参数说明：

* `write|seq|rand`：write表示写操作，seq表示顺序读，rand表示随机读

* `-t`：指定并行IO数。默认为16个并行IO，块大小为4 MB。

* `--no-cleanup`：不要删除测试数据。默认情况下，写完数据后，会自动删除测试数据。
* `--run-name`：指定测试的名称，默认为`benchmark_last_metadata`。

```bash
BENCH OPTIONS:
   -t N
   --concurrent-ios=N
        Set number of concurrent I/O operations
   --show-time
        prefix output with date/time
   --no-verify
        do not verify contents of read objects
   --write-object
        write contents to the objects
   --write-omap
        write contents to the omap
   --write-xattr
        write contents to the extended attributes
```

rados bench会计算三类指标：带宽、IOPS、延迟

对于每类指标，rados bench会输出四个值，分别为平均值、标准差、最大值、最小值。

