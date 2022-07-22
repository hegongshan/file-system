1.下载源码

```bash
git clone https://github.com/HDFGroup/hdf5.git
```

2.创建编译目录

```bash
cd hdf5 && mkdir build && cd build
```

3.配置

```bash
cmake -DHDF5_ENABLE_PARALLEL=ON ..
```

这里添加了`-DHDF5_ENABLE_PARALLEL=ON`选项，以支持并行。HDF5的并行需要安装MPI，否则，将会出现如下错误：

```bash
Could NOT find MPI_C (missing: MPI_C_LIB_NAMES MPI_C_HEADER_DIR MPI_C_WORKS)
```

安装MPI

```bash
yum install -y openmpi3-devel
```

> 1.头文件安装在`/usr/include/openmpi3-x86_64`目录下；
> 2.命令行工具安装在`/usr/lib64/openmpi3/bin`目录下；
> 3.链接库安装在`/usr/lib64/openmpi3/lib`目录下。

4.编译

```bash
make -j12
```

5.安装

```bash
make install
```

默认安装在`/usr/local/HDF_Group/HDF5/${version}/`

6.配置环境变量

```bash
vim ~/.bash_profile
```

添加如下内容：

```bash
# 设置可执行文件搜索路径，以便使用mpicc、h5dump等工具
export PATH=/usr/lib64/openmpi3/bin:/usr/local/HDF_Group/HDF5/1.13.2/bin:$PATH

# 设置C语言头文件搜索路径，以便找到hdf5.h
export C_INCLUDE_PATH=/usr/local/HDF_Group/HDF5/1.13.2/include:$C_INCLUDE_PATH

# 设置C++头文件搜索路径，以便找到hdf5.h
export CPLUS_INCLUDE_PATH=/usr/local/HDF_Group/HDF5/1.13.2/include:$CPLUS_INCLUDE_PATH

# 设置程序编译时的链接库搜索路径，以便找到libhdf5.so
export LIBRARY_PATH=/usr/local/HDF_Group/HDF5/1.13.2/lib:$LIBRARY_PATH

# 设置程序执行时链接库搜索路径
export LD_LIBRARY_PATH=/usr/lib64/openmpi3/lib:/usr/local/HDF_Group/HDF5/1.13.2/lib:$LD_LIBRARY_PATH
```

