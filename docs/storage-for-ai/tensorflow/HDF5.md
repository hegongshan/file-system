# 在TensorFLow中使用HDF5

TensorFlow内部没有支持HDF5。对HDF5的支持，包含在TensorFlow I/O这个项目中。安装TensorFlow I/O：

```bash
pip install tensorflow-io
```

### 参考文献

[1] Example on how to use HDF5 dataset with TFLearn, https://github.com/tflearn/tflearn/blob/master/examples/basics/use_hdf5.py

[2] tfio.IODataset.from_hdf5, https://tensorflow.google.cn/io/api_docs/python/tfio/IODataset#from_hdf5

[3] TensorFlow I/O tests, https://github.com/tensorflow/io/blob/master/tests/test_hdf5.py
