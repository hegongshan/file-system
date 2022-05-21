```bash
rbd bench [--pool <pool>] [--namespace <namespace>] [--image <image>] 
          [--io-size <io-size>] [--io-threads <io-threads>] 
          [--io-total <io-total>] [--io-pattern <io-pattern>] 
          [--rw-mix-read <rw-mix-read>] --io-type <io-type> 
          <image-spec>
```

### 参数说明

可选参数

* `--pool <pool>`：设置测试使用的存储池

* `--image <image>`：设置测试使用的块设备

* `--io-size <io-size>`：设置块大小的字节数，默认为4K。单位为B/K/M/G。最大为4G。
* `--io-threads <io-threads>`：测试使用的线程数，默认为16。
* `--io-total <io-total>`：读写的总大小，默认为1G。单位为 B/K/M/G/T。
* `--io-pattern <io-pattern>`：设置IO访问模式，如随机`rand`、顺序`seq`以及`full-seq`。默认为`seq`。

rand：随机选择

```c++
start_pos = (rand() % (size / io_size)) * io_size;
```

seq：

```c++
uint64_t seq_chunk_length = (size / io_size / io_threads) * io_size;

start_pos = seq_chunk_length * i;
```

full-seq

```c++
start_pos = i * io_size;
```

* `--rw-mix-read <rw-mix-read>`：设置混合读写中读操作的比例，默认为50，即一半读一半写。

* `--io-type <io-type>`：设置IO类型，可选值有`read`、`write`以及`readwrite`(`rw`)。

位置参数

* `<image-spec>`：`[<pool-name>/[<namespace>/]]<image-name>`

### 结果说明

```c++
  std::cout << "bench "
       << " type " << (io_type == IO_TYPE_READ ? "read" :
                       io_type == IO_TYPE_WRITE ? "write" : "readwrite")
       << (io_type == IO_TYPE_RW ? " read:write=" +
           std::to_string(read_proportion) + ":" +
	   std::to_string(100 - read_proportion) : "")
       << " io_size " << io_size
       << " io_threads " << io_threads
       << " bytes " << io_bytes
       << " pattern ";
  switch (io_pattern) {
  case IO_PATTERN_RAND:
    std::cout << "random";
    break;
  case IO_PATTERN_SEQ:
    std::cout << "sequential";
    break;
  case IO_PATTERN_FULL_SEQ:
    std::cout << "full sequential";
    break;
  default:
    ceph_assert(false);
    break;
  }
  std::cout << std::endl;
```

### 动手实践

#### 创建快设备

```bash
rbd create -p test-rbd --size 1T test-rbd-rw
```

#### 写测试

```bash
root@hgs:~# rbd bench --io-type write test-rbd/test-rbd-rw
bench  type write io_size 4096 io_threads 16 bytes 1073741824 pattern sequential
  SEC       OPS   OPS/SEC   BYTES/SEC
    1     28896   29027.5   113 MiB/s
    2     53792   26903.4   105 MiB/s
    3     74208   24773.9    97 MiB/s
    4     99296   24852.3    97 MiB/s
    5    124960   25014.7    98 MiB/s
    6    150176   24197.4    95 MiB/s
    7    173120   23638.2    92 MiB/s
    8    196672   24394.7    95 MiB/s
    9    220768   24274.5    95 MiB/s
   10    245056     23923    93 MiB/s
elapsed: 10   ops: 262144   ops/sec: 23917.8   bytes/sec: 93 MiB/s
```

#### 如何实现混合读写

通过随机函数来判断本次操作应该要执行读操作，还是写操作：

```c++
bool should_read(uint64_t read_proportion)
{
  uint64_t rand_num = rand() % 100;

  if (rand_num < read_proportion)
    return true;
  else
    return false;
}
```

参考资料

[1] https://github.com/ceph/ceph/blob/master/src/tools/rbd/action/Bench.cc