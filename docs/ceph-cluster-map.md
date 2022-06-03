### MON Map

```bash
ceph mon dump
```

输出：

```bash
root@hgs:~# ceph mon dump
epoch 3
fsid e4f78fe8-db95-11ec-a732-1ded39fbeaab
last_changed 2022-05-24T19:34:52.174163+0000
created 2022-05-24T19:17:32.618035+0000
min_mon_release 16 (pacific)
election_strategy: 1
0: [v2:172.16.2.21:3300/0,v1:172.16.2.21:6789/0] mon.xdata1
1: [v2:172.16.2.22:3300/0,v1:172.16.2.22:6789/0] mon.xdata2
2: [v2:172.16.2.26:3300/0,v1:172.16.2.26:6789/0] mon.xdata6
dumped monmap epoch 3
```


### OSD Map

```bash
ceph osd dump
```

### CRUSH Map

```bash
ceph osd crush dump
```

### MDS Map

```bash
ceph fs dump
```


参考资料

[1] CLUSTER MAP, https://docs.ceph.com/en/latest/architecture/#cluster-map
