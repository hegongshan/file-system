### 逻辑卷管理

LVM（Logical Volume Manager，逻辑卷管理）


#### 基本概念

PV：Physical Volume，物理卷

VG：Volume Group，卷组

LV：Logical Volume，逻辑卷

PE：Physical Extent

LE：Logical Extent

图：三层结构

### 实践

|                                 |    PV     |    VG     |    LV     |
| ------------------------------- | :-------: | :-------: | :-------: |
| 创建`*create`                   | pvcreate  | vgcreate  | lvcreate  |
| 查看卷/卷组的概要信息`*s`       |    pvs    |    vgs    |           |
| 列出所有的卷/卷组`*scan`        |  pvscan   |  vgscan   |  lvscan   |
| 查看卷/卷组的详细信息`*display` | pvdisplay | vgdisplay | lvdisplay |
| 扩容`*extend`                   |    ——     | vgextend  | lvextend  |
| 缩容`*reduce`                   |    ——     | vgreduce  | lvreduce  |
| 删除`*remove`                   | pvremove  | vgremove  | lvremove  |

dmsetup

#### 创建分区

创建三个分区：

```bash
fdisk /dev/sdb
```

#### 创建

1.创建物理卷

```bash
pvcreate /dev/sdb2
pvcreate /dev/sdb3
pvcreate /dev/sdb4
```

2.创建卷组

```bash
vgcreate vgdisk /dev/sdb2 /dev/sdb3
```

3.创建逻辑卷

```bash
lvcreate -n test -L 10G vgdisk
```

4.创建文件系统

```bash
mkfs -t ext4 /dev/vgdisk/test
mount /dev/vgdisk/test /mnt/test
```

#### 扩容

1.扩大卷组

```bash
vgextend vgdisk /dev/sdb4
```

2.扩大逻辑卷

```bash
lvextend -L +10G /dev/vgdisk/test
```

3.扩大文件系统

```bash
resize2fs /dev/vgdisk/test
```

#### 缩容

1.卸载文件系统

```bash
umount /mnt/test
```

2.缩小文件系统

```bash
resize2fs /dev/vgdisk/test 10G
mount /dev/vgdisk/test /mnt/test
```

3.缩小逻辑卷

```bash
lvreduce -L 10G /dev/vgdisk/test
```

4.缩小卷组

```bash
vgreduce /dev/vgdisk /dev/sdb4
```

