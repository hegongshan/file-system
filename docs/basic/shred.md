在Linux中，使用rm删除文件，只清除了inode bitmap中的相应位，数据仍然在磁盘上，可以很容易地恢复。

如果要彻底删除文件，可以使用shred：

```bash
shred [OPTION] FILE ...
```

常用选项说明：

* f: 如果有必要的话，修改文件的权限，以便执行写操作。
* n: 覆写的次数，默认为3次。
* u: 覆写完成后，删除文件。
* z: 最后添加一次覆写0，以隐藏粉碎。
* v: 显示覆写进度。

示例：覆写文件

1.数据准备
```bash
$ echo "hello world" > test.txt
$ cat test.txt 
hello world
```

2.覆写文件
```bash
$ shred -vz test.txt 
shred: test.txt: pass 1/4 (random)...
shred: test.txt: pass 2/4 (random)...
shred: test.txt: pass 3/4 (random)...
shred: test.txt: pass 4/4 (000000)...
```

3.检查覆写
```bash
$ cat test.txt # 没有任何输出
$ hexdump test.txt # 文件内容全为0
0000000 0000 0000 0000 0000 0000 0000 0000 0000
*
0001000
```
