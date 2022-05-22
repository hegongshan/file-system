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

在测试的过程中，每秒都会输出如下格式的实时数据：

|     SEC      |             OPS              |    OPS/SEC     |   BYTES/SEC    |
| :----------: | :--------------------------: | :------------: | :------------: |
| 当前是第几秒 | 累计至当前时刻已完成的请求数 | 当前秒内的IOPS | 当前秒内的带宽 |

测试结束后，rbdbench会输出平均IOPS和平均带宽。

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

### 一些疑问

#### 如何实现混合读写

通过随机函数来判断本次操作应该要执行读操作，还是写操作：

```C++
bool should_read(uint64_t read_proportion)
{
  uint64_t rand_num = rand() % 100;

  if (rand_num < read_proportion)
    return true;
  else
    return false;
}
```

#### 如何处理带单位B/K/M/T...的参数？

rbd使用boost的`program_options`来解析命令行参数。`program_options`使用`validate`函数将参数转换为C++类型，rbd bench重载了该函数。

重载的validate函数又调用了

```
src/tools/rbd/action/Bench.cc/validate -> 
src/common/strtol.cc/strict_iecstrtoll -> 
src/common/strtol.cc/strict_iec_cast
```

删除异常处理代码，strict_iec_cast可以简化为：

```c
template<typename T>
T strict_iec_cast(std::string_view str, std::string *err) {
    size_t u = str.find_first_not_of("0123456789-+");
    int m = 0;

    n = str.substr(0, u);
    unit = str.substr(u, str.length() - u);
    // we accept both old si prefixes as well as the proper iec prefixes
    // i.e. K, M, ... and Ki, Mi, ...  
    switch(unit.front()) {
      case 'K': m = 10; break;
      case 'M': m = 20; break;
      case 'G': m = 30; break;
      case 'T': m = 40; break;
      case 'P': m = 50; break;
      case 'E': m = 60; break;
      case 'B': break;
      default:
        *err = "strict_iecstrtoll: unit prefix not recognized";
        return 0;
    }  
    long long ll = strtoll(str.data(), &endptr, base);

    return (ll << m);
}
```

#### 如何处理输出结果中的单位？

```c
std::cout << "elapsed: " << (int)elapsed.count() << "   "
            << "ops: " << ios << "   "
            << "ops/sec: " << (double)ios / elapsed.count() << "   "
            << "bytes/sec: " << byte_u_t((double)off / elapsed.count()) << "/s"
            << std::endl;
```

`byte_u_t`定义在`src/include/types.h`中，其重载了流插入运算符：

```c
inline std::ostream& operator<<(std::ostream& out, const byte_u_t& b)
{
    uint64_t n = b.v;
    int index = 0;
    const char* u[] = {" B", " KiB", " MiB", " GiB", " TiB", " PiB", " EiB"};

    while (n >= 1024 && index < 7) {
      n /= 1024;
      index++;
    }

    return format_u(out, b.v, n, index, 1ULL << (10 * index), u[index]);
}
```





参考资料

[1] https://github.com/ceph/ceph/blob/master/src/tools/rbd/action/Bench.cc