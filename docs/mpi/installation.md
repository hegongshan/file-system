# OpenMPI

### 包管理器安装

* Ubuntu

```bash
apt install -y libopenmpi-dev
```

配置环境变量：

```bash
# 只需要配置头文件的搜索目录
export CPATH=/usr/lib/x86_64-linux-gnu/openmpi/include:$CPATH
```

* CentOS

```bash
yum install -y openmpi3-devel
```

> 对于yum，OpenMPI的安装位置如下：
>
> 1. 头文件安装在`/usr/include/openmpi3-x86_64`目录下；
>
> 2. 命令行工具安装在`/usr/lib64/openmpi3/bin`目录下；
>
> 3. 链接库安装在`/usr/lib64/openmpi3/lib`目录下。

配置环境变量

```bash
# 只需要配置可执行文件的搜索路径
export PATH=/usr/lib64/openmpi3/bin:$PATH
```

