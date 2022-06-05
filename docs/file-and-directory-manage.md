> 前话：最近接手了导师的一个项目，项目部署在Linux服务器上后，出了点小问题，需要修改一些文件。因为不是自己部署的，加之对linux命令不熟，怎么办？在本地修改完后，重新让别人帮忙部署？还是自己在服务器上手动修改？天生不喜欢求人，哈哈

总结一下最近常用的一些Linux基础命令，主要和文件、目录的操作相关

### ls

ls，全称list，用于列出某个目录下的所有文件和目录。

用法：ls -options dirName

参数说明：

* `-a`或者`--all`：列出所有的文件，包括以点号开头的文件

### cd

cd，全称change directory，用于切换路径。估计大伙对cd这个命令都很熟，因为Windows上的命令行界面也是用cd命令来切换路径。

用法：cd [dirName]

值得一提的是，

"~"表示当前用户的主目录，

"."表示当前目录，

".."表示回到当前目录的上一级目录

”-“表示返回之前的目录。例如，当前目录为/usr/local，输入cd /etc后，当前目录为/etc。此时，如果输入cd -，将返回/usr/local，并输出当前目录名。

### pwd

pwd，全称print working directory，用于输出当前的工作目录名。

### rm

rm，全称remove，用于删除文件或目录。

用法：rm [options] name...

参数说明：

* -i 删除前逐一询问确认
* -f 全称force，删除文件不需要询问
* -r 全称recurse，递归删除目录下的所有文件

如果嫌上面的麻烦，那么我们简单粗暴点，删除文件用 rm [fileName]，删除目录用 rm -rf [dirName]，哈哈！

### cp

cp，全称copy，用于复制文件或目录。

用法：cp [options] source... dest

cp的参数比较多，这里只讲-r，其他的用到再现学。cp中的-r和rm中的-r意思差不多，都表示递归。主要用于目录复制。

简单粗暴版：复制单个文件用 cp source ... dest，复制目录用 cp -r [dirName]&nbsp;[dirName]

### mv

mv，具有rename和move两种功能，用于重命名文件或目录，或者将文件或目录移入另一个目录。

用法：mv source dest

简单来说，如果source和dest同为目录或者文件，且dest不存在，则将source重命名为dest；

如果source为文件或目录，而dest为目录，则将source移到dest中。

### touch

touch，用于创建文件。如果文件已经存在，则更新文件的时间戳。

### file

file，用于输出文件类型。

用法：file filename

### mkdir

mkdir，全称make directory，用于创建目录。

用法：mkdir [options] dirName

mkdir的参数暂时没用过，等用到了再补上。

### rmdir

rmdir，全称remove directory，用于删除空目录。

语法：rmdir [options] dirName

参数：

* -p：全称parents，删除指定的空目录后，若此时该目录的父目录已变成空目录，则将父目录一并删除。

