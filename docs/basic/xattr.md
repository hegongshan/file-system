扩展属性（e**X**tend **ATTR**ibutes，xattr）

安装扩展属性管理包：

```bash
# Utilities for managing filesystem extended attributes
# Ubuntu
apt install -y attr

# CentOS
yum install -y attr
```

包含两个命令行工具

1. setfattr

-n name：扩展属性的名称必须按照`namespace.attribute`格式指定。

-v value：指定扩展属性的值

```bash
$ echo "Hello eXtended ATTRibutes" > hello.txt
```

错误的示例：

```bash
$ setfattr -n name -v hgs hello.txt
setfattr: hello.txt: Operation not supported
```

正确的实例：

```bash
$ setfattr -n user.name -v hgs hello.txt
$ getfattr -n user.name hello.txt 
# file: hello.txt
user.name="hgs"

$ getfattr -d hello.txt 
# file: hello.txt
user.name="hgs"

```

删除属性：

```bash
$ setfattr -x user.name hello.txt 
$ getfattr -n user.name hello.txt 
hello.txt: user.name: No such attribute
```



