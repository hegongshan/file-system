# gzip

-d：解压

-l：对每个压缩文件，显示下列字段：

1.压缩后的大小

2.压缩前的大小

3.压缩比

$$
compression\ ratio = \frac{uncompressed\ size- compressed\ size}{uncompressed\ size}
$$

4.未压缩文件的名字

-v：对每个压缩或解压的文件，显示文件名、压缩比例

-k：在执行压缩或者解压缩时，不要删除原始文件

示例：
```bash
$ cat test.txt 
1 2 3 4
5 6 7 8
a b c d
e f g h
```

* 压缩
```bash
$ gzip -k test.txt
$ ls test.txt*
test.txt    test.txt.gz
```

* 解压缩
```bash
$ gzip -d test.txt.gz
```
