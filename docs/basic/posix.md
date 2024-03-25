### 路径

| 相关系统调用 | 对应的Shell命令 |
| ------------ | --------------- |
| dirname      | dirname         |
| basename     | basename        |
| ~~realpath~~ | ~~realpath~~    |
| getcwd       | pwd             |

### 文件与目录

| 文件系统调用     | 对应的Shell命令 | 目录系统调用 | 对应的Shell命令 |
| ---------------- | --------------- | ------------ | --------------- |
| creat            | touch           | mkdir        |                 |
| open             |                 | rmdir        | rmdir           |
| read             |                 | opendir      |                 |
| pread            |                 | readdir      |                 |
| write            |                 |              |                 |
| pwrite           |                 |              |                 |
| lseek            |                 | seekdir      |                 |
| close            |                 | closedir     |                 |
|                  |                 | scandir      |                 |
|                  |                 | rewinddir    |                 |
|                  |                 | telldir      |                 |
|                  |                 | chdir        |                 |
|                  | truncate        |              |                 |
| fallocate        | fallocate       |              |                 |
| fsync、fdatasync |                 |              |                 |

公共：

| 相关系统调用                       | 对应的Shell命令 |
| ---------------------------------- | --------------- |
| rename                             | mv              |
| stat                               | stat            |
| utime、utimes、utimensat、futimens |                 |
| fcntl                              |                 |
| dup                                |                 |
| dup2                               |                 |
| dup3                               |                 |

### 链接

| 相关系统调用 | 头文件   | 对应的Shell命令 |
| ------------ | -------- | --------------- |
| link         | unistd.h | ln、link        |
| symlink      | unistd.h | ln -s           |
| readlink     | unistd.h |                 |
| unlink       | unistd.h | unlink、rm -f   |

### 文件系统

| 相关系统调用    | 对应的Shell命令 |
| --------------- | --------------- |
| mount           | mount           |
| umount          | umount          |
| sync            |                 |
| syncfs          | sync -f         |
| statfs、statvfs | df              |

