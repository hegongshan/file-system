h5import用于将存储在ASCII文件或者二进制文件中的数据转换为HDF5文件中的数据集。

```bash
h5import <infile> -c[onfig] <configfile> [<infile> -c[config] <configfile>...] -o[utfile] <outfile>
```

* infile：输入文件，包含待创建数据集中的数据，可以是一个ASCII，也可以是一个二进制文件
* configfile：配置文件

* outputfile：输出文件，需要修改或创建的HDF5文件

配置文件：

* `PATH`：指定数据集的路径名
* `INPUT-CLASS`：指定输入文件的数据类型
* `RANK`：指定数据集的阶数
* `DIMENSION-SIZES`：指定数据集各个维度的大小，多个维度大小之间使用空格进行分隔
* `OUTPUT-CLASS`：指定输出的数据类型
* `OUTPUT-SIZE`：设置输出的数据大小（位）

示例：

数据：

```bash
$ cat test.txt 
0 1 2 3 4 5 
6 7 8 9 10 11 
12 13 14 15 16 17 
18 19 20 21 22 23
```

配置文件：

```bash
$ cat test.cfg
PATH DS
INPUT-CLASS TEXTIN
RANK 2
DIMENSION-SIZES 4 6
OUTPUT-CLASS IN
OUTPUT-SIZE 32
```

运行结果如下所示：

```bash
$ h5import test.txt -c test.cfg -o test.h5
$ h5dump test.h5
HDF5 "test.h5" {
GROUP "/" {
   DATASET "DS" {
      DATATYPE  H5T_STD_I32LE
      DATASPACE  SIMPLE { ( 4, 6 ) / ( 4, 6 ) }
      DATA {
      (0,0): 0, 1, 2, 3, 4, 5,
      (1,0): 6, 7, 8, 9, 10, 11,
      (2,0): 12, 13, 14, 15, 16, 17,
      (3,0): 18, 19, 20, 21, 22, 23
      }
   }
}
}
```

