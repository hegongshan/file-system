### 格式化

```bash
mkfs [options] [-t type] [fs-options] device [size]
```

### 挂载

```bash
mount -t type device dir
```

### 卸载

```bash
umount {dir|device}
```

开机自动挂载

```bash
vim /etc/fstab
```
### SWAP分区

#### 格式化SWAP

```bash
mkswap <device>
```



参考文献

[1] 刘遄,《Linux就该这样学》,人民邮电出版社
