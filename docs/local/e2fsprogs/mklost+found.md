下载e2fsprogs：

```bash
$ wget https://github.com/tytso/e2fsprogs/archive/refs/tags/v1.46.5.tar.gz
$ tar -zxf v1.46.5.tar.gz
$ cd e2fsprogs-1.46.5/misc/
```

1.变量定义：

```c
#define EXT2_NAME_LEN 255
#define EXT2_NDIR_BLOCKS 12

#define LPF "lost+found"
int main (int argc, char ** argv)
{
    char name[EXT2_NAME_LEN + 2]; 
    char path[sizeof (LPF) + 1 + 256];
    struct stat st; 
    int i, j;
    int d;
```

2.创建目录`lost+found`：

```c
    if (mkdir (LPF, 0700) == -1) {
        perror ("mkdir");
        exit(1);
    }
```

3.创建文件：不断创建大小为0的文件，直到目录`lost+found`占用12个块

```c
    i = 0;
    memset(name, 'x', 246);
    do {
        sprintf(name + 246, "%08d", i);
        strcpy(path, LPF);
        strcat(path, "/");
        strcat(path, name);
        if ((d = creat(path, 0644)) == -1) {
            perror("creat");
            exit(1);
        }
        i++;
        close(d);
        if (stat(LPF, &st) == -1) {
            perror("stat");
            exit(1);
        }
    } while (st.st_size <= (EXT2_NDIR_BLOCKS - 1) * st.st_blksize);
```

4.删除创建的文件

```c
    for (j = 0; j < i; j++) {
        sprintf (name + 246, "%08d", j);
        strcpy (path, LPF);
        strcat (path, "/");
        strcat (path, name);
        if (unlink (path) == -1) {
            perror ("unlink");
            exit (1);
        }
    }
    exit (0);
}
```

从上面的代码可以看出：`mklost+found`的作用在于创建一个目录`lost+found`，让它占用12个块。
