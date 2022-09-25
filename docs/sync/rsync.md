rsync（**r**emote **sync**hronous，远程同步）是一个文件同步工具。

选项：

* `-a`，`--archive`：归档模式，即递归处理子目录，保留软链接，保持文件属性不变，包括时间、权限、组名、用户名等信息
* `-z`，`--compress`：在传输文件的时候，对数据进行压缩
* `--partial`：保存部分传输的文件。默认情况下，如果传输过程被打断，rsync会删除部分完成的文件。
* `--progress`：显示同步进度条
* `-P`：等同于`--partial --progress`
* `-v`：输出详细信息
* `-n`，`--dry-run`：只显示哪些文件和目录将会被传输，不进行实际的数据传输

示例：

将远程主机`139.255.123.3`的`/root/test`，同步到本机的`/home/hgs`下

```bash
rsync -azP root@139.255.123.3:/root/test /home/hgs
```

问题：有scp等文件传输命令，为什么还需要rsync？

> rsync可以实现增量同步

