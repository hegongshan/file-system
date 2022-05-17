本文记录了使用cephadm安装ceph，以及挂载和测试的全过程。

### 基本概念

| 缩写  |                             全称                             |         补充说明          |
| :---: | :----------------------------------------------------------: | :-----------------------: |
| RADOS | Reliable Autonomic Distributed Object Store，可靠的、自动化的、分布式的对象存储 |                           |
|  RBD  |                      RADOS Block Device                      |          块存储           |
|  RGW  |                        RADOS GateWay                         |    对象存储RESTful API    |
|  OSD  |             Object Storage Device，对象存储设备              | 四种状态：up/down和in/out |
|  PG   |                       Placement Group                        |                           |
|  MDS  |                MetaData Server，元数据服务器                 |                           |
|  mon  |                           Monitor                            |                           |
|  mgr  |                           Manager                            | 有active和standby两种状态 |

### 前期准备

安装Docker和Python3。

```shell
yum install -y docker-ce python3
```

### 安装ceph

1.安装cephadm

https://docs.ceph.com/en/pacific/cephadm/install/#curl-based-installation

2.设置引导节点

```shell
cephadm bootstrap --mon-ip <ip address>
```

3.安装ceph-common工具

```shell
cephadm add-repo --release octopus
cephadm install ceph-common
```

之后，就可以使用ceph命令了。

### 添加主机

1.将公钥复制到其他主机中

```shell
ssh-copy-id -f -i /etc/ceph/ceph.pub root@<hostname>
```

公钥将被添加到`~/.ssh/authorized_keys`文件中。

2.将主机添加到集群中

```shell
ceph orch host add <hostname> [<ip>] [<label1> ...]
```

3.查看集群中的所有节点

```shell
ceph orch host ls
```

如果需要删除主机，可以使用如下命令：

```shell
ceph orch host rm <hostname>
```

如果需要更新主机的地址，可以使用：

```shell
orch host set-addr <hostname> <addr>
```

4.为便于管理，可以为主机添加标签

```shell
ceph orch host label add *<host>* _admin
ceph orch host label rm *<host>* _admin
```

### 添加额外的mon节点

```shell
ceph orch apply mon <hostname>
```

删除mon节点

```shell
ceph mon rm <name>
```

### 添加OSD

1.查看所有可用的设备

```shell
ceph orch apply osd --all-available-devices --dry-run
```

2.添加OSD，共有如下两种方式：

第一，自动添加OSD，为所有可用的设备添加OSD

```shell
ceph orch apply osd --all-available-devices
```

第二，手动添加OSD

```shell
ceph orch daemon add osd *<host>*:*<device-path>*
```

3.查看现有的存储设备

```shell
ceph orch device ls
```

4.删除现有的OSD

```shell
ceph orch osd rm <osd_id(s)>
```

5.查看集群中的OSD

```shell
ceph osd ls
```

### 部署CephFS

1.创建文件系统

```shell
# ceph fs volume create <fs_name>
ceph fs volume create cephfs --placement="<placement spec>"
```

> 2.手动创建
>
> 首先，创建元数据和数据存储池
>
> ```shell
> ceph osd pool create cephfs_data
> ceph osd pool create cephfs_metadata
> ```
>
> 删除存储池：
>
> ```shell
> ceph osd pool rm <pool> [<pool>] [--yes-i-really-really-mean-it]
> ```
>
> 然后，创建文件系统
>
> ```shell
> # ceph fs new <fs_name> <metadata> <data>
> ceph fs new cephfs cephfs_metadata cephfs_data
> ```
> 
> 查看存储池信息：
> 
> ```shell
> ceph osd pool ls detail
> ```

2.部署MDS

创建描述文件：

```yaml
service_type: mds
service_id: fs_name
placement:
  count: 3
```

手动部署MDS：

```shell
ceph orch apply -i mds.yaml
```

查看MDS状态：

```shell
ceph mds stat
```

3.查看文件系统状态

```shell
# ceph fs status <fs_name>
ceph fs status cephfs
```

4.删除mds

https://docs.ceph.com/en/pacific/cephfs/add-remove-mds/#removing-an-mds

5.删除文件系统

```shell
ceph fs volume rm <vol_name> --yes-i-really-mean-it
```

### 查看Ceph

1.查看ceph集群的状态

```shell
# ceph status
ceph -s
```

2.查看ceph集群的健康状态

```shell
ceph health [detail]
```

3.查看ceph集群中所有的守护进程

```shell
ceph orch ps
```

4.查看集群中的服务

```shell
ceph orch lsceph
```

### 测试读写

1.挂载前的准备

https://docs.ceph.com/en/pacific/cephfs/mount-prerequisites/

2.挂载

* 使用内核驱动进行[挂载](https://docs.ceph.com/en/pacific/cephfs/mount-using-kernel-driver/)。

在客户端中，需要安装`ceph-common`，否则无法直接解析mon：

```shell
[19986.950242] libceph: Failed to parse monitor IPs: -22
[20036.764549] libceph: Failed to parse monitor IPs: -22
[20407.695311] libceph: Failed to parse monitor IPs: -22
[21540.415601] libceph: Failed to parse monitor IPs: -22
[70653.559713] libceph: Failed to parse monitor IPs: -22
```

安装ceph-common时，记得使用cephadm安装较新的版本。不要使用yum，因为其安装的是较老的版本。

```shell
mount -t ceph :/ /mnt/cephfs -o name=<name>
```

当然，我们也可以不安装`ceph-common`，那便需要在挂载时手动指定mon的信息。

```shell
mount -t ceph 192.168.0.1:6789,192.168.0.2:6789:/ /mnt/mycephfs -o name=foo,secret=AQATSKdNGBnwLhAAnNDKnH65FmVKpXZJVasUeQ==
```

其中，`<name>`和`<secret>`来自于`/etc/ceph/ceph.client.<name>.keyring `。

* 使用FUSE进行[挂载](https://docs.ceph.com/en/pacific/cephfs/mount-using-fuse/)

首先，安装ceph-fuse

```shell
yum install -y ceph-fuse
```

然后，挂载CephFS

```shell
ceph-fuse --id 用户名 挂载点
# ceph-fuse -n client.用户名 挂载点
```

3.修改/etc/fstab

### Ceph运行中可能出现的问题

问题一：

```shell
HEALTH_WARN clock skew detected on mon.xxx
```
