h5dump是一个命令行工具，用于查看HDF5文件的内容。

* -n：查看内容
* -a A：查看属性
* -d D：查看数据集
* -g G：查看组
* -B：查看超级块
* --header, -H：查看对象头信息（元数据）

Hyperslab相关选项：

* -s, --start start：子集的开始位置
* -S, --stride stride：Hyperslab步幅
* -c, --count count：块的个数
* -k, --block block：块大小

示例：

以HDF5官网上的分块数据集文件为例：

```bash
wget https://support.hdfgroup.org/ftp/HDF5/examples/files/exbyapi/h5ex_d_chunk.h5
```

1.查看文件结构

```bash
h5dump -n h5ex_d_chunk.h5
HDF5 "h5ex_d_chunk.h5" {
FILE_CONTENTS {
 group      /
 dataset    /DS1
 }
}
```

2.查看整个文件

```bash
$ h5dump h5ex_d_chunk.h5
HDF5 "h5ex_d_chunk.h5" {
GROUP "/" {
   DATASET "DS1" {
      DATATYPE  H5T_STD_I32LE
      DATASPACE  SIMPLE { ( 6, 8 ) / ( 6, 8 ) }
      DATA {
      (0,0): 0, 1, 0, 0, 1, 0, 0, 1,
      (1,0): 1, 1, 0, 1, 1, 0, 1, 1,
      (2,0): 0, 0, 0, 0, 0, 0, 0, 0,
      (3,0): 0, 1, 0, 0, 1, 0, 0, 1,
      (4,0): 1, 1, 0, 1, 1, 0, 1, 1,
      (5,0): 0, 0, 0, 0, 0, 0, 0, 0
      }
   }
}
}
```

3.查看某个数据集

```bash
$ h5dump -d "/DS1" h5ex_d_chunk.h5
HDF5 "h5ex_d_chunk.h5" {
DATASET "/DS1" {
   DATATYPE  H5T_STD_I32LE
   DATASPACE  SIMPLE { ( 6, 8 ) / ( 6, 8 ) }
   DATA {
   (0,0): 0, 1, 0, 0, 1, 0, 0, 1,
   (1,0): 1, 1, 0, 1, 1, 0, 1, 1,
   (2,0): 0, 0, 0, 0, 0, 0, 0, 0,
   (3,0): 0, 1, 0, 0, 1, 0, 0, 1,
   (4,0): 1, 1, 0, 1, 1, 0, 1, 1,
   (5,0): 0, 0, 0, 0, 0, 0, 0, 0
   }
}
}
```

4.查看数据集子集

```bash
$ h5dump -d "/DS1" -s "0,0" -S "4,4" -c "2,2" -k "2,2" h5ex_d_chunk.h5 
HDF5 "h5ex_d_chunk.h5" {
DATASET "/DS1" {
   DATATYPE  H5T_STD_I32LE
   DATASPACE  SIMPLE { ( 6, 8 ) / ( 6, 8 ) }
   SUBSET {
      START ( 0, 0 );
      STRIDE ( 4, 4 );
      COUNT ( 2, 2 );
      BLOCK ( 2, 2 );
      DATA {
      (0,0): 0, 1, 1, 0,
      (1,0): 1, 1, 1, 0,
      (4,0): 1, 1, 1, 0,
      (5,0): 0, 0, 0, 0
      }
   }
}
}
```

简短形式：h5dump支持使用语法`dataset[start;stride;count;block]`来选择子集。

```bash
$ h5dump -d "/DS1[0,0;4,4;2,2;2,2]" h5ex_d_chunk.h5 
HDF5 "h5ex_d_chunk.h5" {
DATASET "/DS1" {
   DATATYPE  H5T_STD_I32LE
   DATASPACE  SIMPLE { ( 6, 8 ) / ( 6, 8 ) }
   SUBSET {
      START ( 0, 0 );
      STRIDE ( 4, 4 );
      COUNT ( 2, 2 );
      BLOCK ( 2, 2 );
      DATA {
      (0,0): 0, 1, 1, 0,
      (1,0): 1, 1, 1, 0,
      (4,0): 1, 1, 1, 0,
      (5,0): 0, 0, 0, 0
      }
   }
}
}
```

5.仅查看对象头信息

```bash
$ h5dump -H -d "/DS1" h5ex_d_chunk.h5
HDF5 "h5ex_d_chunk.h5" {
DATASET "/DS1" {
   DATATYPE  H5T_STD_I32LE
   DATASPACE  SIMPLE { ( 6, 8 ) / ( 6, 8 ) }
}
}
```

