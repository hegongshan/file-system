### 创建文件

* C

```c
hid_t H5Fcreate(const char *filename, unsigned flags, hid_t fcpl_id, hid_t fapl_id);
```

位于`H5Fpublic.h`，参数说明：

filename：文件名

flags：访问模式

fcpl_id：File Creation Property List，文件创建属性列表

fapl_id：File Access Property List，文件访问属性列表

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

* Python3

```python
import h5py

file = h5py.File('test.h5', 'w')
file.close()
```

### 创建数据集

* C

```c
hid_t H5Screate_simple(int rank, const hsize_t dims[], const hsize_t maxdims[]);
```

位于`H5Spublic.h`中，参数说明：

rank：维度个数

dims：每个维度的大小

maxdims：每个维度的最大大小

```c
hid_t H5Dcreate2(hid_t loc_id, const char *name, hid_t type_id, hid_t space_id, hid_t lcpl_id, hid_t dcpl_id, hid_t dapl_id);
```





```c
#include <hdf5.h>

int main()
{
   hid_t   file_id, dataset_id, dataspace_id;
   hsize_t dims[2];
   herr_t  status;

   file_id = H5Fcreate("test.h5", H5F_ACC_TRUNC, H5P_DEFAULT, H5P_DEFAULT);

   dims[0] = 4;
   dims[1] = 6;
   dataspace_id = H5Screate_simple(2, dims, NULL);

   dataset_id = H5Dcreate(file_id, "/dset", H5T_STD_I32BE, dataspace_id, H5P_DEFAULT, H5P_DEFAULT, H5P_DEFAULT);

   status = H5Dclose(dataset_id);
   status = H5Sclose(dataspace_id);

}
```

* Python3

```python
import h5py

file = h5py.File('test.h5', 'w')
file.create_dataset("dset", shape=(4, 6), dtype=h5py.h5t.STD_I32BE)
file.close()
```

### 创建组

### 创建属性

