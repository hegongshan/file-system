1.下载源码

```bash
git clone --recurse-submodules https://github.com/hpc-io/h5bench.git
```

2.创建编译目录

```bash
cd h5bench && mkdir build && cd build
```

3.配置，需要编译所有的Benchmark

```bash
cmake -DH5BENCH_ALL=ON ..
```

4.编译

```bash
make -j12
```

5.安装

```bash
make install
```

参考资料

1.Build Instructions，https://github.com/hpc-io/h5bench/blob/master/docs/source/buildinstructions.rst
