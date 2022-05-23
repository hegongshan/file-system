查看磁盘信息

### lsblk

全称为list block，可以查看机器中的块设备。

### lsscsi

scsi：Small Computer System Interface，小型计算机系统接口

如何查看服务器使用的磁盘类型呢？

lsscsi，全称为list small computer system interface，用于列出SCSI设备和NVMe设备及相关属性。

### 安装lsscsi

默认情况下，服务器不会安装lsscsi工具。因此，需要手动安装。

```bash
# CentOS
yum install -y lsscsi

# Ubuntu
apt install -y lsscsi
```

### 查看存储设备类型

```bash
lsscsi
设备类型 厂家 型号 版本 设备名
```

* 查看存储设备的容量

```bash
lsscsi -s
```
