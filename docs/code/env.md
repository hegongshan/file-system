在实现存储系统时，可能需要读写环境变量，例如，通过环境变量设置某些参数的值。

因此，我们需要知道如何在代码中读写环境变量。与环境变量相关的函数声明位于`stdlib.h`中。

#### 读取环境变量的值

```c
/* Return the value of envariable NAME, or NULL if it doesn't exist.  */
char *getenv (const char *__name);
/* This function is similar to the above but returns NULL if the
   programs is running with SUID or SGID enabled.  */
char *secure_getenv (const char *__name);
```

* 测试

1.设置环境变量

```shell
$ export LOG_PATH=/var/log/fs.log
```

2.读取环境变量`test_env.c`

```c
#include <stdio.h>
#include <stdlib.h>

int main(int argc, const char *args[]) 
{
    char *log_path = getenv("LOG_PATH");
    printf("log_path = %s\n", log_path);
    return 0;
}
```

3.编译并执行

```shell
$ gcc test_env.c -o test_env.out
$ ./test_env.out 
log_path = /var/log/fs.log
```

#### 设置环境变量的值

```c
/* Set NAME to VALUE in the environment.
   If REPLACE is nonzero, overwrite an existing value.  */
int setenv (const char *__name, const char *__value, int __replace);
```

* 测试

1.代码`test_setenv.c`如下：

```c
#include <stdio.h>
#include <stdlib.h>

int main(int argc, const char *args[]) 
{
    if (setenv("LOG_PATH", "/var/log/fs.log", 1)) {
        printf("Failed to set environment variable\n");
        return 0;
    }

    char *log_path = getenv("LOG_PATH");
    printf("log_path = %s\n", log_path);
    return 0;
}
```

2.编译并执行

```shell
$ gcc test_setenv.c -o test_setenv.out
$ ./test_setenv.out 
log_path = /var/log/fs.log
```

#### 删除环境变量

```c
/* Remove the variable NAME from the environment.  */
int unsetenv (const char *__name);
```

* 测试

1.编写测试代码`test_unsetenv.c`

```c
#include <stdio.h>
#include <stdlib.h>

int main(int argc, const char *args[]) 
{
    if (setenv("LOG_PATH", "/var/log/fs.log", 1)) {
        printf("Failed to set environment variable\n");
        return 0;
    }

    char *log_path = getenv("LOG_PATH");
    printf("log_path = %s\n", log_path);
    
    if (unsetenv("LOG_PATH")) {
        printf("Failed to unset environment variable\n");
        return 0;
    }
    
    log_path = getenv("LOG_PATH");
    printf("log_path = %s\n", log_path);
    return 0;
}
```

2.编译并执行

```shell
$ gcc test_unsetenv.c -o test_unsetenv.out
$ ./test_unsetenv.out 
log_path = /var/log/fs.log
log_path = (null)
```

