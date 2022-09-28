### 添加OSD

```bash
ceph orch daemon add osd *<host>*:*<device-path>* 
```

### 查看OSD

```bash
ceph osd tree
ceph osd ls
ceph osd stat
ceph osd status
```

### 管理OSD的状态

```bash
# 1.踢出集群
ceph osd out <ids>

# 2.服务下线，停止运行
ceph osd down <ids>

# 3.加入集群
ceph osd in <ids>
```

暂停OSD

```bash
sudo systemctl stop ceph-osd@{osd-num}
```

### 删除OSD

```bash
# 1.从CRUSH Map中删除OSD
ceph osd crush rm <name>

# 2.删除OSD的认证
ceph auth del osd.{osd-num}

# 3.删除OSD
ceph osd rm <ids>
```

### 参考资料

1. Adding/Removing OSDs, https://docs.ceph.com/en/latest/rados/operations/add-or-rm-osds/
