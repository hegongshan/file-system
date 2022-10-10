### 写操作

#### 创建文件

```python
import h5py

file = h5py.File('test.h5', 'w')
file.close()
```

#### 创建组

```python
import h5py

file = h5py.File('test.h5', 'w')
file.create_group('group')
file.close()
```

#### 创建数据集

方法一：通过文件和组对象的`create_dataset()`

```python
import h5py

file = h5py.File('test.h5', 'w')
group = file.create_group('group')
# dset = group.create_dataset("dset", shape=(2, 3), dtype=h5py.h5t.STD_I32BE, data=[[1, 2, 3], [4, 5, 6]])
dset = group.create_dataset("dset", shape=(2, 3), dtype=h5py.h5t.STD_I32LE)
dset[...] = [[1, 2, 3], [4, 5, 6]]
file.close()
```

方法二：通过文件和组对象的`require_dataset()`

```python
import h5py

file = h5py.File('test.h5', 'w')
group = file.create_group('group')
dset = group.require_dataset("dset", shape=(2, 3), dtype=h5py.h5t.STD_I32LE)
dset[...] = [[1, 2, 3], [4, 5, 6]]
file.close()
```

方法三：直接赋值

```python
import h5py

file = h5py.File('test.h5', 'w')
group = file.create_group('group')
group['dset'] = [[1, 2, 3], [4, 5, 6]]
file.close()
```

#### 创建属性

在HDF5中，组和数据集均可以创建自己的属性，通过`<obj>.attrs`方式可以读写属性，attrs可以当作Python中的字典来使用。

```python
import h5py

file = h5py.File('test.h5', 'w')
group = file.create_group('group')
ds = group.create_dataset("dset", shape=(2, 3), dtype=h5py.h5t.STD_I32LE)
ds.attrs['created_time'] = '2022-10-10 11:16:00'
file.close()
```

### 读操作

```python
import h5py

file = h5py.File('test.h5', 'r')
print(file['/group/dset'][...])
file.close()
```



### 参考资料

1.HDF5 for Python, https://docs.h5py.org