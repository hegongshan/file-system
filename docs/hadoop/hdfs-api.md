### HDFS Java API

HDFS的API位于类`org.apache.hadoop.fs.FileSystem`中。

### 创建操作

* 目录

```java
public boolean mkdirs(Path f) throws IOException;
```

* 文件

```java
public FSDataOutputStream create(Path f) throws IOException;
```

### 读取操作

* 读取文件，方法签名如下：

```java
 public FSDataInputStream open(Path f) throws IOException;
```

* 读取目录，方法签名如下：

```java
public abstract FileStatus[] listStatus(Path f) throws FileNotFoundException, IOException;
```

### 修改操作

* 追加操作的方法签名如下：

````java
public FSDataOutputStream append(Path f) throws IOException;
````

* 截断操作的方法签名如下：

```java
public boolean truncate(Path f, long newLength) throws IOException;
```

### 删除操作

方法签名如下：

```java
public abstract boolean delete(Path f, boolean recursive) throws IOException;
```

示例：

```java
FileSystem fs = FileSystem.get(conf);
Path output = new Path(outputPath);
if (fs.exists(output)) {
    fs.delete(output, true);
}
```

### 上传和下载

* 上传

```java
public void copyFromLocalFile(Path src, Path dst);
```

* 下载

```java
public void copyToLocalFile(Path src, Path dst) throws IOException;
```

