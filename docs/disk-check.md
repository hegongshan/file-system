### badblocks

检查设备中的坏块。

```shell
badblocks [options] <device> <last_block> <first_block>
```

选项：

-s：显示扫描的进度

-v：显示详细信息

-b block_size：块大小，以字节为单位，默认为1024字节。

-c number_of_blocks：一次测试的块数，默认为64。

-w：写入数据。默认情况下，badblocks只会进行读测试。

示例：读测试

![](/img/badblocks-with-option-s.png)

### smartctl

安装SMART管理工具，

Self-Monitoring, Analysis and Reporting Technology (S.M.A.R.T)，自动检测、分析与报告技术。

```shell
# Ubuntu
apt install -y smartmontools

# CentOS
yum install -y smartmontools
```

1.查看SMART是否开启

```shell
smartctl --info <device>
```

2.开启和关闭SMART能力

```shell
smartctl --smart=on <device>
smartctl --smart=off <device>
```

3.查看SMART数据

```shell
smartctl --all <device>
smartctl -a <device>
```

4.查看健康状态

```shell
smartctl -H <device>
smartctl --health <device>
```

