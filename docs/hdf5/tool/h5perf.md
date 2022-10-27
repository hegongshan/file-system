h5perf用于测试并行HDF5在不同配置下的读写带宽。

环境变量：

* HDF5_NOCLEANUP：如果设置了该变量，h5perf将不会删除测试文件。默认情况下，h5perf会删除测试文件。

选项：

* `-A api_list`：设置需要测试的API类型，可选的值为posix、mpiio以及phdf5，如`-A posix,mpiio`
* `-F N`：设置需要创建的文件数量
* `-d N`：设置每个文件中的数据集个数
* `-i N`：设置需要执行的迭代次数
* `-p N`（小写字母*p*）：设置需要使用的最小进程数
* `-P N`（大写字母P）：设置需要使用的最大进程数
* `-e size, --num-bytes=size`：设置每个数据集中，每个进程需要读写的字节数（*bytes-per-process*）
* `-x size`：设置传输缓冲区（transfer buffer）的最小大小
* `-X size`：设置传输缓冲区（transfer buffer）的最大大小
* `-B size`：设置缓冲区中的块大小
* `-I, --interleaved`：使用interleaved block I/O，默认采用Contiguous block I/O
* `-C, --collective`：使用Collective I/O，默认采用Independent I/O
* `-g, --geometry`：使用2D Geometry进行测试，默认采用1D Geometry
* `-c, --chunk`：使用分块布局创建数据集，默认采用连续布局。
* `-w`, `--write-only`：只执行写测试，不执行读测试，默认先执行写测试再执行读测试。

```bash
$ h5perf
HDF5 Library: Version 1.12.2
rank 0: ==== Parameters ====
rank 0: IO API=posix mpiio phdf5 
rank 0: Number of files=1
rank 0: Number of datasets=1
rank 0: Number of iterations=1
rank 0: Number of processes=1:1
rank 0: Number of bytes per process per dataset=256KB
rank 0: Size of dataset(s)=256KB:256KB
rank 0: File size=256KB:256KB
rank 0: Transfer buffer size=128KB:256KB
rank 0: Block size=128KB
rank 0: Block Pattern in Dataset=Contiguous
rank 0: I/O Method for MPI and HDF5=Independent
rank 0: Geometry=1D
rank 0: VFL used for HDF5 I/O=MPI-IO driver
rank 0: Data storage method in HDF5=Contiguous
rank 0: Env HDF5_PARAPREFIX=not set
rank 0: Dumping MPI Info Object (up to 256 bytes per item):
object is MPI_INFO_NULL
rank 0: ==== End of Parameters ====

Number of processors = 1
Transfer Buffer Size: 131072 bytes, File size: 0.25 MB
      # of files: 1, # of datasets: 1, dataset size: 0.25 MB
        IO API = POSIX
            Write (1 iteration(s)):
                Maximum Throughput: 939.37 MB/s
                Average Throughput: 939.37 MB/s
                Minimum Throughput: 939.37 MB/s
            Write Open-Close (1 iteration(s)):
                Maximum Throughput: 772.79 MB/s
                Average Throughput: 772.79 MB/s
                Minimum Throughput: 772.79 MB/s
            Read (1 iteration(s)):
                Maximum Throughput: 1545.81 MB/s
                Average Throughput: 1545.81 MB/s
                Minimum Throughput: 1545.81 MB/s
            Read Open-Close (1 iteration(s)):
                Maximum Throughput: 1490.45 MB/s
                Average Throughput: 1490.45 MB/s
                Minimum Throughput: 1490.45 MB/s
        IO API = MPIO
            Write (1 iteration(s)):
                Maximum Throughput: 1100.41 MB/s
                Average Throughput: 1100.41 MB/s
                Minimum Throughput: 1100.41 MB/s
            Write Open-Close (1 iteration(s)):
                Maximum Throughput:  97.33 MB/s
                Average Throughput:  97.33 MB/s
                Minimum Throughput:  97.33 MB/s
            Read (1 iteration(s)):
                Maximum Throughput: 1463.82 MB/s
                Average Throughput: 1463.82 MB/s
                Minimum Throughput: 1463.82 MB/s
            Read Open-Close (1 iteration(s)):
                Maximum Throughput: 561.85 MB/s
                Average Throughput: 561.85 MB/s
                Minimum Throughput: 561.85 MB/s
        IO API = PHDF5 (w/MPI-IO driver)
            Write (1 iteration(s)):
                Maximum Throughput: 458.01 MB/s
                Average Throughput: 458.01 MB/s
                Minimum Throughput: 458.01 MB/s
            Write Open-Close (1 iteration(s)):
                Maximum Throughput: 140.56 MB/s
                Average Throughput: 140.56 MB/s
                Minimum Throughput: 140.56 MB/s
            Read (1 iteration(s)):
                Maximum Throughput: 1318.50 MB/s
                Average Throughput: 1318.50 MB/s
                Minimum Throughput: 1318.50 MB/s
            Read Open-Close (1 iteration(s)):
                Maximum Throughput: 251.39 MB/s
                Average Throughput: 251.39 MB/s
                Minimum Throughput: 251.39 MB/s
Transfer Buffer Size: 262144 bytes, File size: 0.25 MB
      # of files: 1, # of datasets: 1, dataset size: 0.25 MB
        IO API = POSIX
            Write (1 iteration(s)):
                Maximum Throughput: 1250.96 MB/s
                Average Throughput: 1250.96 MB/s
                Minimum Throughput: 1250.96 MB/s
            Write Open-Close (1 iteration(s)):
                Maximum Throughput: 1116.47 MB/s
                Average Throughput: 1116.47 MB/s
                Minimum Throughput: 1116.47 MB/s
            Read (1 iteration(s)):
                Maximum Throughput: 2935.20 MB/s
                Average Throughput: 2935.20 MB/s
                Minimum Throughput: 2935.20 MB/s
            Read Open-Close (1 iteration(s)):
                Maximum Throughput: 2735.77 MB/s
                Average Throughput: 2735.77 MB/s
                Minimum Throughput: 2735.77 MB/s
        IO API = MPIO
            Write (1 iteration(s)):
                Maximum Throughput: 1279.41 MB/s
                Average Throughput: 1279.41 MB/s
                Minimum Throughput: 1279.41 MB/s
            Write Open-Close (1 iteration(s)):
                Maximum Throughput: 462.01 MB/s
                Average Throughput: 462.01 MB/s
                Minimum Throughput: 462.01 MB/s
            Read (1 iteration(s)):
                Maximum Throughput: 4632.98 MB/s
                Average Throughput: 4632.98 MB/s
                Minimum Throughput: 4632.98 MB/s
            Read Open-Close (1 iteration(s)):
                Maximum Throughput: 793.70 MB/s
                Average Throughput: 793.70 MB/s
                Minimum Throughput: 793.70 MB/s
        IO API = PHDF5 (w/MPI-IO driver)
            Write (1 iteration(s)):
                Maximum Throughput: 709.13 MB/s
                Average Throughput: 709.13 MB/s
                Minimum Throughput: 709.13 MB/s
            Write Open-Close (1 iteration(s)):
                Maximum Throughput: 248.63 MB/s
                Average Throughput: 248.63 MB/s
                Minimum Throughput: 248.63 MB/s
            Read (1 iteration(s)):
                Maximum Throughput: 1614.99 MB/s
                Average Throughput: 1614.99 MB/s
                Minimum Throughput: 1614.99 MB/s
            Read Open-Close (1 iteration(s)):
                Maximum Throughput: 377.19 MB/s
                Average Throughput: 377.19 MB/s
                Minimum Throughput: 377.19 MB/s
```

### 参考文献

1. H5PERF, https://portal.hdfgroup.org/display/HDF5/h5perf
2. h5perf User Guide, https://support.hdfgroup.org/HDF5/doc/Tools/h5perf_parallel/h5perf_parallel.pdf
