BeeOND (*Bee*GFS *On* *D*emand) 是一个Burst Buffer文件系统，其架构如下图所示：

![](../img/beeond-overview.png)

### 准备工作

1.安装pdsh

```bash
$ pdsh -R ssh -w node14,node27 yum install -y epel-release
$ pdsh -R ssh -w node14,node27 yum install -y pdsh
```

2.下载包仓库文件，以`BeeGFS v7.2.8`为例

```bash
$ pdsh -R ssh -w node14,node27 wget -O /etc/yum.repos.d/beegfs-rhel7.repo https://www.beegfs.io/release/beegfs_7.2.8/dists/beegfs-rhel7.repo
```

### 安装BeeOND

```bash
$ pdsh -R ssh -w node14,node27 yum install -y beeond
```

此时，若启动BeeOND，将会产生如下错误：

```bash
Unrecoverable error: No connAuthFile configured. Using BeeGFS without connection authentication is considered insecure and is not recommended. If you really want or need to run BeeGFS without connection authentication, please set connDisableAuthentication to true.
```

### 配置连接认证

根据上面的错误提示，我们继续[配置连接认证](https://doc.beegfs.io/latest/advanced_topics/authentication.html)。

1.生成共享秘钥

```bash
$ dd if=/dev/random of=/etc/beegfs/connauthfile bs=128 count=1
$ chown root:root /etc/beegfs/connauthfile
$ chmod 400 /etc/beegfs/connauthfile
```

2.将`/etc/beegfs/connauthfile`复制到集群中的所有节点

```bash
$ pdcp -R ssh -w node14,node27 /etc/beegfs/connauthfile /etc/beegfs/connauthfile
```

3.对于集群中的每个节点，修改所有服务的配置文件，指定连接认证文件的位置`connAuthFile`

```bash
$ pdsh -R ssh -w node14,node27 "sed -in 's/^connAuthFile.*$/connAuthFile=\/etc\/beegfs\/connauthfile/g' /etc/beegfs/beegfs-*.conf"
```

### 运行BeeOND

1.生成nodefile文件，每行指定一台主机

```bash
$ cat << EOF > nodefile
node14
node27
EOF
```

2.启动BeeOND

```bash
$ beeond start -n nodefile -d /data/beeond -c /mnt/beeond -f /etc/beegfs -P
```

注意，在启动时，必须指定`-f PATH`。否则，即使我们已经设置了`connAuthFile`或`connDisableAuthentication`，仍然会产生`No connAuthFile configured`错误。

### 关闭BeeOND

```bash
$ beeond stop -n nodefile -L -d
```

### 参考文献

1. https://doc.beegfs.io/latest/quick_start_guide/quick_start_guide.html

1. https://doc.beegfs.io/latest/advanced_topics/beeond.html

1. https://doc.beegfs.io/latest/advanced_topics/authentication.html
