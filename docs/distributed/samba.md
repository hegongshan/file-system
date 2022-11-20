Server Message Block (SMB)/Common Internet File System (CIFS)

### 配置Linux服务器

1.安装Samba

```bash
apt install -y samba
```

2.创建用户

```bash
$ smbpasswd -a hgs
New SMB password:
Retype new SMB password:
Added user hgs.
```

使用smbpasswd创建用户时，用户名必须在本机上已经存在，即用useradd创建过。

如果用户不存在，将会创建失败，提示信息如下所示（test用户事先不存在）：

```bash
$ smbpasswd -a test
New SMB password:
Retype new SMB password:
Failed to add entry for user test.
```

3.配置Samba

```bash
$ mkdir /tmp/public
$ chmod 777 /tmp/public
$ cat >> /etc/samba/smb.conf << EOF
[share]
writable = yes
path = /tmp/public
create mask = 0777
directory mask = 0777
EOF
```

`[share]`表示共享资源的名字，里面的参数定义了共享的属性。部分参数的说明如下：

| 参数                     | 说明                                                         | 参数                    | 说明                                                         |
| ------------------------ | ------------------------------------------------------------ | ----------------------- | ------------------------------------------------------------ |
| read only =yes&#124;no   | 只读，默认为yes                                              | available = yes&#124;no | 是否开启服务，默认为yes，即允许访问                          |
| writable = yes&#124;no   | 可写，read only的反义词。默认为no                            | path                    | 设置需要共享的目录                                           |
| browseable = yes&#124;no | 该共享能否在网络视图和浏览列表中可见，默认为yes。若为no，则看不到该目录，但可以直接访问该目录。 | create mask             | 设置创建文件时的权限掩码，默认为0744                         |
| guest ok = yes&#124;no   | 是否允许以访客的身份连接服务，默认为no                       | directory mask          | 设置创建目录时的权限掩码，默认为0755                         |
| public = yes&#124;no     | 与`guest ok`含义相同                                         | valid users             | 指定允许登录的用户列表，用户名之间使用逗号分隔。默认为空，即允许所有用户登录。 |

4.启动SMB服务

```bash
$ systemctl start smbd
```

### Windows

在资源管理器的地址栏中，输入如下内容：

```bash
\\192.168.19.10\public
```

### macOS

使用方法如下：

```bash
$ mount -t smbfs //user@server/share path
```

示例：

```bash
$ mount -t smbfs //hgs@192.168.19.10/public ~/public
```

注意，`share`是指不带路径名的共享目录名。如果不小心加上了路径名，将产生如下错误：

```bash
$ mount -t smbfs //hgs@192.168.19.10/tmp/public ~/public
Password for 192.168.19.10: 
mount_smbfs: mount error: /Users/hegongshan/public: Unknown error: -1073741275
mount: /Users/hegongshan/public failed with 64
```

### 参考文献

1. https://www.samba.org/samba/docs/current/man-html/smb.conf.5.html
