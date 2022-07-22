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

可能出现的错误：

```bash
Could NOT find PNetCDF (missing: PNETCDF_LIBRARIES PNETCDF_INCLUDES)
```

这是因为E3SM-IO Benchmark需要安装pnetcdf：

```bash
wget https://parallel-netcdf.github.io/Release/pnetcdf-1.12.2.tar.gz
tar -zxf pnetcdf-1.12.2.tar.gz
cd pnetcdf-1.12.2
./configure --prefix=/usr/local CC=mpicc
make -j12 install
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

2.Parallel I/O Kernel Case Study -- E3SM Software Requirements，https://github.com/Parallel-NetCDF/E3SM-IO/tree/4e34c0b02093407fea1540d1263e13acb69eba83
