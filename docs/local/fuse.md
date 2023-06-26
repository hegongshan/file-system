**F**ilesystem in **Use**rspace (FUSE)



### 使用

卸载基于FUSE的文件系统

```bash
fusermount -u <MOUNTPOINT>
```

如果上面的命令输出如下错误信息:

```bash
fusermount: failed to unmount <MOUNTPOINT>: Device or resource busy
```

那么，可以使用下面的命令来卸载文件系统：

```bash
fusermount -uz <MOUNTPOINT>
```

参考文献

[1] To FUSE or Not to FUSE: Performance of User-Space File Systems, FAST'17
