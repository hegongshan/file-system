

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

运行结果如下：

```bash
$ h5cc test-hdf5-file-create.c -o test-file-create.out
$ ./test-file-create.out
$ h5dump test.h5
HDF5 "test.h5" {
GROUP "/" {
}
}
```

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

#### 创建组

方法签名如下：

```c
hid_t H5Gcreate(hid_t loc_id, const char *name, hid_t lcpl_id, hid_t gcpl_id, hid_t gapl_id);
```

示例：

```c
#include <hdf5.h>

#define DIM0 4
#define DIM1 6

int main(int argc, char const *argv[])
{
    hid_t file_id, group_id, dataset_id, space_id;
    herr_t status;
    int dset_data[DIM0][DIM1];

    // 1.打开文件
    file_id = H5Fopen("test.h5", H5F_ACC_RDWR, H5P_DEFAULT);
    
    // 2.创建组
    group_id = H5Gcreate(file_id, "MyGroup", H5P_DEFAULT, H5P_DEFAULT, H5P_DEFAULT);

    // 3.在组中创建数据集
    hsize_t dims[] = {DIM0, DIM1};
    space_id = H5Screate_simple(2, dims, NULL);
    dataset_id = H5Dcreate(group_id, "DS1", H5T_STD_I32LE, space_id, H5P_DEFAULT, H5P_DEFAULT, H5P_DEFAULT);
    
    // 4.关闭并释放资源
    status = H5Sclose(space_id);
    status = H5Dclose(dataset_id);
    status = H5Gclose(group_id);
    status = H5Fclose(file_id);
    return 0;
}

```

运行结果如下：

```bash
$ h5cc test-hdf5-group-create.c -o test-group-create.out              
$ ./test-group-create.out 
$ h5dump -g /MyGroup test.h5
HDF5 "test.h5" {
GROUP "/MyGroup" {
   DATASET "DS1" {
      DATATYPE  H5T_STD_I32LE
      DATASPACE  SIMPLE { ( 4, 6 ) / ( 4, 6 ) }
      DATA {
      (0,0): 0, 0, 0, 0, 0, 0,
      (1,0): 0, 0, 0, 0, 0, 0,
      (2,0): 0, 0, 0, 0, 0, 0,
      (3,0): 0, 0, 0, 0, 0, 0
      }
   }
}
}
```

#### 创建属性

属性有自己的数据类型和数据空间。

```c
hid_t H5Acreate2(
    hid_t loc_id, 
    const char *attr_name, 
    hid_t type_id, 
    hid_t space_id, 
    hid_t acpl_id, 
    hid_t aapl_id
);
herr_t H5Awrite(
    hid_t attr_id,
    hid_t mem_type_id,
    const void *buf
);
```

`H5Acreate()`是映射向`H5Acreate2()`的宏。

示例：

```c
#include <hdf5.h>

int main(int argc, char const *argv[])
{
    hid_t file_id, group_id, dataset_id, attr_space_id, attr_id;
    herr_t status;
    hsize_t dims;
    int attr_data[2];

    // 1.打开文件、组和数据集
    file_id = H5Fopen("test.h5", H5F_ACC_RDWR, H5P_DEFAULT);
    group_id = H5Gopen(file_id, "MyGroup", H5P_DEFAULT);
    dataset_id = H5Dopen(group_id, "DS1", H5P_DEFAULT);

    // 2.创建数据集属性
    dims = 2;
    attr_data[0] = 100;
    attr_data[1] = 200;
    attr_space_id = H5Screate_simple(1, &dims, NULL);
    if (!H5Aexists(dataset_id, "Units")) {
        attr_id = H5Acreate(dataset_id, "Units", H5T_STD_I32LE, attr_space_id, H5P_DEFAULT, H5P_DEFAULT);

        // 3.写属性数据
        status = H5Awrite(attr_id, H5T_NATIVE_INT, attr_data);
    }
    
    // 4.关闭并释放资源
    status = H5Aclose(attr_id);
    status = H5Sclose(attr_space_id);
    status = H5Dclose(dataset_id);
    status = H5Gclose(group_id);
    status = H5Fclose(file_id);
    return 0;
}
```

运行结果如下：

```bash
$ h5cc test-hdf5-attribute-create.c -o test-attribute-create.out
$ ./test-attribute-create.out
$ h5dump -a /MyGroup/DS1/Units test.h5
HDF5 "test.h5" {
ATTRIBUTE "Units" {
   DATATYPE  H5T_STD_I32LE
   DATASPACE  SIMPLE { ( 2 ) / ( 2 ) }
   DATA {
   (0): 100, 200
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

#### 读取组

方法签名如下：

```c
hid_t H5Gopen(hid_t loc_id, const char * name, hid_t gapl_id);
herr_t H5Gget_info(hid_t group_id, H5G_info_t *group_info);
```

示例：

```c
#include <hdf5.h>

int main(int argc, char const *argv[])
{
    hid_t file_id, group_id;
    herr_t status;
    H5G_info_t ginfo;

    // 1.打开文件
    file_id = H5Fopen("test.h5", H5F_ACC_RDWR, H5P_DEFAULT);
    
    // 2.打开组
    group_id = H5Gopen(file_id, "MyGroup", H5P_DEFAULT);
    status = H5Gget_info(group_id, &ginfo);
    printf("Storage Type (0, 1 and 2 denote symbol tables, compact and indexed storage, respectively): %d\n", ginfo.storage_type);
    printf("Number of links: %lld\n", ginfo.nlinks);
    
    // 3.关闭并释放资源
    status = H5Gclose(group_id);
    status = H5Fclose(file_id);
    return 0;
}

```

#### 读取属性

示例：

```c
#include <hdf5.h>

#define DIM 2

int main(int argc, char const *argv[])
{
    hid_t file_id, dataset_id, attr_id, attr_space_id;
    herr_t status;
    int rank, attr_data[DIM];
    hsize_t dims;
    H5S_class_t space_type;

    // 1.打开文件、数据集以及属性
    file_id = H5Fopen("test.h5", H5F_ACC_RDWR, H5P_DEFAULT);
    dataset_id = H5Dopen(file_id, "/MyGroup/DS1", H5P_DEFAULT);
    attr_id = H5Aopen(dataset_id, "Units", H5P_DEFAULT);

    // 2.读取属性
    status = H5Aread(attr_id, H5T_NATIVE_INT, attr_data);
    for (int i = 0; i < DIM; i++) {
        printf("%d\n", attr_data[i]);
    }

    // 3.读取属性的数据空间
    attr_space_id = H5Aget_space(attr_id);
    rank = H5Sget_simple_extent_dims(attr_space_id, &dims, NULL);
    space_type = H5Sget_simple_extent_type(attr_space_id);
    printf("Rank = %d, Dims = %lld, Type of Dataspace (1 denotes simple dataset) = %d\n", rank, dims, space_type);

    // 4.关闭并释放资源
    status = H5Sclose(attr_space_id);
    status = H5Aclose(attr_id);
    status = H5Dclose(dataset_id);
    status = H5Fclose(file_id);
    return 0;
}
```

### 删除操作

#### 删除属性

如果要删除属性，需要使用`H5Adelete()`方法签名如下：

```c
herr_t H5Adelete(hid_t loc_id, const char *attr_name);
```

示例：

```c
#include <hdf5.h>

int main(int argc, char const *argv[])
{
    hid_t file_id, dataset_id;
    herr_t status;

    // 1.打开文件和数据集
    file_id = H5Fopen("test.h5", H5F_ACC_RDWR, H5P_DEFAULT);
    dataset_id = H5Dopen(file_id, "/MyGroup/DS1", H5P_DEFAULT);

    // 2.删除属性
    status = H5Adelete(dataset_id, "Units");
    
    // 3.关闭并释放资源
    status = H5Dclose(dataset_id);
    status = H5Fclose(file_id);
    return 0;
}
```

执行结果如下：

```bash
$ h5dump -A test.h5
HDF5 "test.h5" {
GROUP "/" {
   GROUP "MyGroup" {
      DATASET "DS1" {
         DATATYPE  H5T_STD_I32LE
         DATASPACE  SIMPLE { ( 4, 6 ) / ( 4, 6 ) }
         ATTRIBUTE "Units" {
            DATATYPE  H5T_STD_I32LE
            DATASPACE  SIMPLE { ( 2 ) / ( 2 ) }
            DATA {
            (0): 100, 200
            }
         }
      }
   }
}
}
$ h5cc test_h5_attribute_delete.c -o test_attribute_delete.out
$ ./test_attribute_delete.out 
$ h5dump -A test.h5
HDF5 "test.h5" {
GROUP "/" {
   GROUP "MyGroup" {
      DATASET "DS1" {
         DATATYPE  H5T_STD_I32LE
         DATASPACE  SIMPLE { ( 4, 6 ) / ( 4, 6 ) }
      }
   }
}
}
```

#### 删除数据集/组

如果要删除数据集或组，需要使用`H5Ldelete()`，方法签名如下：

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
   GROUP "MyGroup" {
      DATASET "DS1" {
         DATATYPE  H5T_STD_I32LE
         DATASPACE  SIMPLE { ( 4, 6 ) / ( 4, 6 ) }
         DATA {
         (0,0): 0, 0, 0, 0, 0, 0,
         (1,0): 0, 0, 0, 0, 0, 0,
         (2,0): 0, 0, 0, 0, 0, 0,
         (3,0): 0, 0, 0, 0, 0, 0
         }
      }
   }
}
}
```

#### 删除文件

如果要删除HDF5文件，需要使用`H5Fdelete()`，方法签名如下：

```c
herr_t H5Fdelete(const char *filename, hid_t fapl_id);
```

> 函数H5Fdelete()在HDF5 1.12中引入，以支持Virtual Object Layer (VOL)，适配多种存储系统。
>
> 然而，原生的HDF5存储Native VOL Connector并没有支持该函数。如果要使用该函数，必须使用1.13及之后的版本。

```c
#include <hdf5.h>

int main(int argc, char const *argv[])
{
    herr_t status;

    status = H5Fdelete("test.h5", H5P_DEFAULT);
    return 0;
}
```

