### 写入操作

#### 创建文件

> H5F_CREATE，https://portal.hdfgroup.org/display/HDF5/H5F_CREATE

方法签名如下：

```c
hid_t H5Fcreate(const char *filename, unsigned flags, hid_t fcpl_id, hid_t fapl_id);
```

位于`H5Fpublic.h`，参数说明：

* filename：文件名

* flags：访问模式

| 访问模式      | 描述                                             |
| ------------- | ------------------------------------------------ |
| H5F_ACC_TRUNC | 如果文件存在，则覆写文件；如果不存在，则创建文件 |
| H5F_ACC_EXCL  | 如果文件存在，则失败                             |

* fcpl_id：File Creation Property List identifier，文件创建属性列表

* fapl_id：File Access Property List identifier，文件访问属性列表

示例：

```c
#include <hdf5.h>

int main() 
{
    hid_t       file_id;
    herr_t      status;

    file_id = H5Fcreate("test.h5", H5F_ACC_TRUNC, H5P_DEFAULT, H5P_DEFAULT);

    status = H5Fclose(file_id);
}
```

> H5P_DEFAULT表示使用相应属性列表的默认值

#### 创建数据集

```c
hid_t H5Screate_simple(int rank, const hsize_t dims[], const hsize_t maxdims[]);
```

位于`H5Spublic.h`中，参数说明：

* rank：维度个数

* dims：每个维度的大小

* maxdims：每个维度的最大大小

```c
hid_t H5Dcreate2(hid_t loc_id, const char *name, hid_t type_id, hid_t space_id, hid_t lcpl_id, hid_t dcpl_id, hid_t dapl_id);
```

* name：可以用文件中的绝对路径，也可以用针对loc_id的相对路径
* lcpl_id：Link Creation Property List

H5Dcreate是一个宏，指向H5Dcreate2。

```c
herr_t H5Dwrite(hid_t dataset_id, hid_t mem_type_id, hid_t mem_space_id, hid_t file_space_id, hid_t xfer_plist_id, const void * buf); 
```

```c
#include <hdf5.h>

#define DIM0 4
#define DIM1 6

int main(int argc, char const *argv[])
{
    hid_t file_id, dataspace_id, dataset_id;
    herr_t status;
    int dset_data[DIM0][DIM1];

    // 1.创建文件
    file_id = H5Fcreate("test.h5", H5F_ACC_TRUNC, H5P_DEFAULT, H5P_DEFAULT);
    
    // 2.创建dataspace
    hsize_t dims[] = {DIM0, DIM1};
    dataspace_id = H5Screate_simple(2, dims, NULL);

    // 3.创建数据集
    dataset_id = H5Dcreate(file_id, "/dset", H5T_STD_I32LE, dataspace_id, H5P_DEFAULT, H5P_DEFAULT, H5P_DEFAULT);

    // 4.写入数据
    for (int i = 0; i < DIM0; i++) {
        for (int j = 0; j < DIM1; j++) {
            dset_data[i][j] = i * 6 + j + 1;
        }
    }
    status = H5Dwrite(dataset_id, H5T_NATIVE_INT, H5S_ALL, H5S_ALL, H5P_DEFAULT, dset_data);
    
    // 5.关闭并释放资源
    status = H5Sclose(dataspace_id);
    status = H5Dclose(dataset_id);
    status = H5Fclose(file_id);
    return 0;
}
```

查看生成的HDF5文件：

```bash
$ h5cc test-hdf5-dataset-write.c -o test-dataset-write.out
$ h5dump test.h5
HDF5 "test.h5" {
GROUP "/" {
   DATASET "dset" {
      DATATYPE  H5T_STD_I32LE
      DATASPACE  SIMPLE { ( 4, 6 ) / ( 4, 6 ) }
      DATA {
      (0,0): 1, 2, 3, 4, 5, 6,
      (1,0): 7, 8, 9, 10, 11, 12,
      (2,0): 13, 14, 15, 16, 17, 18,
      (3,0): 19, 20, 21, 22, 23, 24
      }
   }
}
}
```

### 读取操作

#### 读文件

> H5F_OPEN，https://portal.hdfgroup.org/display/HDF5/H5F_OPEN

方法签名如下：

```c
hid_t H5Fopen(const char *name, unsigned flags, hid_t fapl_id);
herr_t H5Dread(hid_t dataset_id, hid_t mem_type_id, hid_t mem_space_id, hid_t file_space_id, hid_t xfer_plist_id, void * buf);
```

#### 读数据集

```c
#include <stdio.h>

#include <hdf5.h>

#define DIM0 4
#define DIM1 6

int main(int argc, char const *argv[])
{
    hid_t file_id, dataset_id;
    herr_t status;
    int dset_data[DIM0][DIM1];

    // 1.打开文件和数据集
    file_id = H5Fopen("test.h5", H5F_ACC_RDWR, H5P_DEFAULT);
    dataset_id = H5Dopen(file_id, "/dset", H5P_DEFAULT);

    // 2.读取数据集中的数据
    status = H5Dread(dataset_id, H5T_NATIVE_INT, H5S_ALL, H5S_ALL, H5P_DEFAULT, dset_data);
    for (int i = 0; i < DIM0; i++) {
        printf (" [");
        for (int j = 0; j < DIM1; j++) {
            printf(" %3d", dset_data[i][j]);
        }
        printf ("]\n");
    }
    
    // 3.关闭并释放资源
    status = H5Dclose(dataset_id);
    status = H5Fclose(file_id);
    return 0;
}
```

输出结果如下：

```bash
$ h5cc test-hdf5-dataset-read.c -o test-dataset-read.out
$ ./test-dataset-read.out
 [   1   2   3   4   5   6]
 [   7   8   9  10  11  12]
 [  13  14  15  16  17  18]
 [  19  20  21  22  23  24]
```

### 删除操作

在HDF5中，没有专门的`H5Ddelete`和`H5Gdelete`函数。如果要删除数据集或组，需要使用`H5Ldelete`，方法签名如下：

```c
htri_t H5Lexists(hid_t loc_id, const char *name, hid_t lapl_id);
herr_t H5Ldelete(hid_t loc_id, const char *name, hid_t lapl_id);
```

示例：

```c
#include <hdf5.h>

int main(int argc, char const *argv[])
{
    hid_t file_id;
    herr_t status;

    file_id = H5Fopen("test.h5", H5F_ACC_RDWR, H5P_DEFAULT);
    if (H5Lexists(file_id, "/dset", H5P_DEFAULT)) {
        H5Ldelete(file_id, "/dset", H5P_DEFAULT);
    }
    status = H5Fclose(file_id);
    return 0;
}
```

执行结果如下：

```bash
$ h5cc test-hdf5-dataset-delete.c -o test-dataset-delete.out
$ ./test-dataset-delete.out 
$ h5dump test.h5
HDF5 "test.h5" {
GROUP "/" {
}
}
```



