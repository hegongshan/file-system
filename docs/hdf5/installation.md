### 包管理器安装

* Ubuntu

> 1.hdf5的开发包会依赖hdf5的运行库libhdf5-xxx（libhdf5-openmpi-xxx）和编译工具hdf5-helpers（包括h5cc、h5c++以及h5fc）
>
> 2.hdf5提供的运行时工具（如h5ls、h5dump等）包含在hdf5-tools中

```bash
# 串行版本
apt install -y libhdf5-dev hdf5-tools

# OpenMPI版本
apt install -y libhdf5-openmpi-dev hdf5-tools
```

* CentOS

> hdf5：包含库文件和运行时工具
>
> hdf5-devel：包含hdf5的头文件和编译工具

```bash
# 串行版本
yum install -y hdf5 hdf5-devel

# OpenMPI版本
yum install -y hdf5-openmpi3 hdf5-openmpi3-devel
```

### 源码安装

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

安装MPI：

```bash
# Ubuntu
apt install -y libopenmpi-dev
```

> 对于apt，OpenMPI的安装位置如下：
>
> 1.`/usr/lib/x86_64-linux-gnu/openmpi/`

```bash
# CentOS
yum install -y openmpi3-devel
```

> 对于yum，OpenMPI的安装位置如下：
>
> 1.头文件安装在`/usr/include/openmpi3-x86_64`目录下；
>
> 2.命令行工具安装在`/usr/lib64/openmpi3/bin`目录下；
>
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

# 设置程序执行时的链接库搜索路径，以便找到libhdf5.so
export LD_LIBRARY_PATH=/usr/lib64/openmpi3/lib:/usr/local/HDF_Group/HDF5/1.13.2/lib:$LD_LIBRARY_PATH
```

