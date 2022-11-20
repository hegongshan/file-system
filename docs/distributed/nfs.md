1985年，Sun公司提出了网络文件系统（Network File System，NFS）协议。它采用了客户端-服务器架构，允许多个客户端通过计算机网络共享文件服务器中的文件。

```bash
服务端：192.168.2.10
客户端：192.168.2.11
```

### 服务端配置

1.安装NFS和RPC工具包

> RPC：Remote Procedure Call，远程方法调用

```bash
# CentOS
yum install -y nfs-utils rpcbind
```

2.创建共享目录

```bash
mkdir /data
chown -R nfsnobody:nfsnobody /data
```

3.修改配置文件

```bash
vim /etc/exports
```

文件格式如下：`共享目录 允许访问的主机(权限)`

```bash
/data 192.168.2.11(rw,sync)
```

权限：

```bash
ro：readonly

rw：readwrite

sync：synchronization

async：asynchronization

no_root_squash

root_squash

all_squash

anonuid：anonymous uid

anongid：anonymous gid
```

4.启动NFS和RPC服务

```bash
# CentOS
systemctl enable nfs rpcbind
systemctl start nfs rpcbind
```

### 客户端配置

1.安装RPC工具包

```bash
# CentOS
yum install -y rpcbind nfs-utils
```

2.启动RPC服务

```bash
systemctl start rpcbind
```

3.挂载NFS服务

```bash
mount -t nfs 192.168.2.10:/data /mnt/nfs
```

4.查看挂载情况

```bash
df -h
```

5.设置开机自动挂载

```bash
echo '192.168.2.10:/data /mnt/nfs nfs defaults 0 0' >> /etc/fstab
```

参考资料

[1] Design and Implementation of the Sun Network Filesystem, USENIX Summer'85
