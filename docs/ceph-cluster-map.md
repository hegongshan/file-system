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

src/mon/MonMap.h

```c++
class MonMap {
  public:
    epoch_t epoch;       // what epoch/version of the monmap
    uuid_d fsid;
    utime_t last_changed;
    utime_t created;

    std::map<std::string, mon_info_t> mon_info;
    // 存储了每个mon的名字，如[xdata1,xdata2,xdata6]
    std::vector<std::string> ranks;
    enum election_strategy {
      // Keep in sync with ElectionLogic.h!
      CLASSIC = 1, // the original rank-based one
      DISALLOW = 2, // disallow a set from being leader
      CONNECTIVITY = 3 // includes DISALLOW, extends to prefer stronger connections
  };
  election_strategy strategy = CLASSIC;
  std::set<std::string> disallowed_leaders; // can't be leader under CONNECTIVITY/DISALLOW
}
```

src/mon/MonMap.cc

MonMap::print

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
