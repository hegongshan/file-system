`h5ls`，类似于Unix中的`ls`，用于查看HDF5的层次结构。

```bash
h5ls [OPTIONS] file[/OBJECT] [file[/[OBJECT]...]
```

* `-r`,` --recursive`：递归处理所有的组；
* `-v`, `--verbose`：输出更详细的信息；
* `-a`, `--address`：输出原始数据的地址，需要和`-v`选项组合使用；
* `-d`, `--data`：输出数据集中的数据。

如果数据集是连续存储的，返回原始数据的起始位置在HDF5文件中的偏移量。

如果数据集采用分块存储，将输出一个地址列表，表示每个分块起始位置的偏移量。

以HDF5官网中的两个文件为例：

```bash
wget -q https://support.hdfgroup.org/ftp/HDF5/examples/files/exbyapi/h5ex_g_visit.h5
wget -q https://support.hdfgroup.org/ftp/HDF5/examples/files/exbyapi/h5ex_d_chunk.h5 
```

1.输出HDF5文件`h5ex_g_visit.h5`的层次结构

```bash
$ h5ls -r h5ex_g_visit.h5
/                        Group
/group1                  Group
/group1/dset1            Dataset {1, 1}
/group1/group3           Group
/group1/group3/dset2     Dataset, same as /group1/dset1
/group1/group3/group4    Group
/group1/group3/group4/group1 Group
/group1/group3/group4/group1/group5 Group, same as /group1
/group1/group3/group4/group2 Group
/group2                  Group, same as /group1/group3
```

2.输出文件`h5ex_d_chunk.h5`中数据集`DS1`分块数据的地址

```bash
$ h5ls -a -v h5ex_d_chunk.h5/DS1
Opened "h5ex_d_chunk.h5" with sec2 driver.
DS1                      Dataset {6/6, 8/8}
    Location:  1:800
    Links:     1
    Chunks:    {4, 4} 64 bytes
    Storage:   192 logical bytes, 256 allocated bytes, 75.00% utilization
    Type:      native int
    Address: 1400
           Flags    Bytes     Address          Logical Offset
        ========== ======== ========== ==============================
        0x00000000       64       4016 [0, 0, 0]
        0x00000000       64       4080 [0, 4, 0]
        0x00000000       64       4144 [4, 0, 0]
        0x00000000       64       4208 [4, 4, 0]
```

3.输出文件`h5ex_d_chunk.h5`中数据集`DS1`的值

```bash
h5ls -d h5ex_d_chunk.h5/DS1
DS1                      Dataset {6, 8}
    Data:
         0, 1, 0, 0, 1, 0, 0, 1, 1, 1, 0, 1, 1, 0, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 1, 0, 0, 1, 1, 1, 0, 1, 1,
         0, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0
```

### 参考文献

1. H5LS, https://portal.hdfgroup.org/display/HDF5/h5ls

