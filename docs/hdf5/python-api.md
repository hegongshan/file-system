### 写入操作

#### 创建文件

```python
import h5py

file = h5py.File('test.h5', 'w')
file.close()
```

#### 创建组

```python
import h5py

with h5py.File('test.h5', 'w') as file:
	file.create_group('group')
```

#### 创建数据集

方法一：通过文件和组对象的`create_dataset()`

```python
import h5py

for i in range(4):
	for j in range(6):
		dset_data[i][j] = i * 6 + j + 1

with h5py.File('test.h5', 'w') as file:
	group = file.create_group('group')
	# dset = group.create_dataset("dset", shape=(4, 6), dtype=h5py.h5t.STD_I32BE, data=dset_data)
	dset = group.create_dataset("dset", shape=(4, 6), dtype=h5py.h5t.STD_I32LE)
	dset[...] = dset_data
```

方法二：通过文件和组对象的`require_dataset()`

```python
import h5py

for i in range(4):
	for j in range(6):
		dset_data[i][j] = i * 6 + j + 1

with h5py.File('test.h5', 'w') as file:
	group = file.create_group('group')
	dset = group.require_dataset("dset", shape=(4, 6), dtype=h5py.h5t.STD_I32LE)
	dset[...] = dset_data
```

方法三：直接赋值

```python
import h5py

for i in range(4):
	for j in range(6):
		dset_data[i][j] = i * 6 + j + 1

with h5py.File('test.h5', 'w') as file:
	group = file.create_group('group')
	group['dset'] = dset_data
```

#### 创建属性

在HDF5中，组和数据集均可以创建自己的属性，通过`<obj>.attrs`方式可以读写属性，attrs可以当作Python中的字典来使用。

```python
import h5py

with h5py.File('test.h5', 'w') as file:
	group = file.create_group('group')
	dset = group.create_dataset("dset", shape=(2, 3), dtype=h5py.h5t.STD_I32LE)
	dset.attrs['created_time'] = '2022-10-10 11:16:00'
```

### 读取操作

```python
import h5py

with h5py.File('test.h5', 'r') as file:
  dset = file['/group/dset']
	print(dset[...])
```

### 删除操作

如果要删除文件中的对象（比如数据集、组等），使用Python的标准语法`del <obj>`即可。

示例：删除数据集

```python
import h5py

with h5py.File('test.h5', 'w') as file:
	if '/dset' in file:
		del file['/dset']
```

运行结果如下：

```bash
$ python3 test-delete.py
$ h5dump test.h5
HDF5 "test.h5" {
GROUP "/" {
}
}
```

### 参考资料

1.HDF5 for Python, https://docs.h5py.org