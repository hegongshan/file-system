BeeGFS内置了一个测试存储性能的测试工具StorageBench：


```bash
$ beegfs-ctl --storagebench --help
```

测试对象

* `--alltargets`：测试所有的存储目标（storage target）；
* `--targetids=<targetlist>`：逗号分隔的targetIDs列表，只测试特定的存储目标；
* `--servers=<nodelist>`：逗号分隔的nodeIDs列表，测试特定服务中的所有存储目标。

动作类型

* `--write`：执行写测试；
* `--read`：执行读测试；
* `--stop`：停止正在运行的测试；
* `--status`：输出测试的运行状态或执行结果；
* `--cleanup`：从存储目标中删除生成的测试文件。

测试设置

* `--blocksize=<blocksize>`：读/写测试的块大小，默认为128K；
* `--size=<size>`：每个线程处理的总文件大小，默认为1G；
* `--threads=<threadcount>`：每个存储目标中文件的数量。

可选项

* `--verbose`：打印每个Storage Server的测试结果；
* `--wait`：等待测试执行结束；
* `--odirect `：在存储服务器上使用直接I/O（O_DIRECT）。

### 参考文献

1. Benchmarking a BeeGFS System, https://doc.beegfs.io/latest/advanced_topics/benchmark.html
