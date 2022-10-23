RAID，全称为Redundancy Array of Independent Disk，独立磁盘冗余阵列。

mdadm是Linux中软件RAID的管理工具。其中，md的全称为multiple devices。

古人云：工欲善其事，必先利其器。想要使用软件RAID，则需要先安装mdadm：

```bash
yum install -y mdadm
```

### 创建RAID

```shell
# mdadm --create
mdadm -C /dev/md0 -n 4 -l 10 /dev/sdb{1..4}
```

其中，

-n, --raid-devices：指定RAID中的活跃磁盘数；

-l, --level：设置RAID级别；

最后则是磁盘设备名称列表。

### 管理RAID

* 添加一个磁盘

```shell
mdadm /dev/md0 --add /dev/sdb
```

* 删除一个磁盘

```shell
mdadm /dev/md0 --fail /dev/sdb1 --remove /dev/sdb1
```

### 查看RAID

* 查看所有的RAID

```shell
cat /proc/mdstat
```

* 查看某个RAID

```shell
mdadm -D /dev/md0
```

### 删除RAID

1.查看RAID中的磁盘信息，记下设备号

```bash
mdadm -D /dev/md0
```

2.卸载RAID

```shell
umount /dev/md0
```

3.逐一删除所有磁盘设备

```bash
mdadm /dev/md0 --fail /dev/sdb1 --remove /dev/sdb1
mdadm /dev/md0 --fail /dev/sdc1 --remove /dev/sdc1
```

4.停用RAID

```shell
# mdadm --stop /dev/md0
mdadm -S /dev/md0
```

5.删除配置文件

```shell
rm -f /etc/mdadm.conf
```

6.删除fstab中的配置

```shell
vim /etc/fstab
```

### 重新组装RAID

在停用RAID后，如果想要重新启动RAID，则可以使用如下命令：

```
mdadm -A -s /dev/md0
```

