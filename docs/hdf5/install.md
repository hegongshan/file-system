### 包管理器安装

* Ubuntu

> libhdf5-dev：包含HDF5头文件和静态库
>
> 1. 依赖动态库libhdf5-xxx（libhdf5-openmpi-xxx）
> 2. 依赖C++版的动态库libhdf5-cpp-xxx
> 3. 依赖编译工具hdf5-helpers，包含h5cc、h5c++以及h5fc
>
> hdf5-tools：包含HDF5提供的运行时工具（如h5ls、h5dump等）

```bash
# 串行版本
apt install -y libhdf5-dev hdf5-tools

# OpenMPI版本
apt install -y libhdf5-openmpi-dev hdf5-tools
```

* CentOS

> hdf5：包含HDF5动态库和运行时工具
>
> hdf5-devel：包含HDF5头文件和编译工具
>
> hdf5-static：包含HDF5静态库

```bash
# 串行版本
yum install -y hdf5 hdf5-devel hdf5-static

# OpenMPI版本
yum install -y hdf5-openmpi3 hdf5-openmpi3-devel hdf5-openmpi3-static
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
# 若不需要MPI并行，即只需要串行执行，则不要加该选项
cmake -DHDF5_ENABLE_PARALLEL=ON ..
```

这里添加了`-DHDF5_ENABLE_PARALLEL=ON`选项，以支持并行。HDF5的并行需要安装MPI，否则，将会出现如下错误：

```bash
Could NOT find MPI_C (missing: MPI_C_LIB_NAMES MPI_C_HEADER_DIR MPI_C_WORKS)
```

安装MPI参看[安装MPI](../mpi/install.md)

4.编译

```bash
make -j12
```

5.安装

```bash
make install
```

默认安装在`/usr/local/HDF_Group/HDF5/${version}/`

此时，使用HDF5自带的h5cc/h5pcc等编译工具，可以正常编译并执行程序。

6.配置环境变量，以便使用GCC编译HDF5程序和HDF5的运行时工具

```bash
vim ~/.bash_profile
```

添加如下内容：

```bash
# 设置可执行文件搜索路径，以便使用h5dump等工具
export PATH=/usr/local/HDF_Group/HDF5/1.13.2/bin:$PATH

# 设置C/C++语言头文件搜索路径，以便找到hdf5.h
export CPATH=/usr/local/HDF_Group/HDF5/1.13.2/include:$CPATH

# 设置C语言头文件搜索路径，以便找到hdf5.h
# export C_INCLUDE_PATH=/usr/local/HDF_Group/HDF5/1.13.2/include:$C_INCLUDE_PATH

# 设置C++头文件搜索路径，以便找到hdf5.h
# export CPLUS_INCLUDE_PATH=/usr/local/HDF_Group/HDF5/1.13.2/include:$CPLUS_INCLUDE_PATH

# 设置程序编译时的链接库搜索路径，以便找到libhdf5.so和libhdf5.a等
export LIBRARY_PATH=/usr/local/HDF_Group/HDF5/1.13.2/lib:$LIBRARY_PATH

# 设置程序执行时的链接库搜索路径，以便找到libhdf5.so
export LD_LIBRARY_PATH=/usr/local/HDF_Group/HDF5/1.13.2/lib:$LD_LIBRARY_PATH
```

### 参考文献

1.INSTALL_CMake.txt, https://github.com/HDFGroup/hdf5/blob/develop/release_docs/INSTALL_CMake.txt
