访问控制列表（Access Control List，ACL）

```bash
# Ubuntu
apt install -y acl

# CentOS
yum install -y acl
```



```bash
$ echo "Hello Access Control Lists" > hello.txt
$ getfacl hello.txt 
# file: hello.txt
# owner: root
# group: root
user::rw-
group::r--
other::r--

```

