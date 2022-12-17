下面以`Hadoop 3.3.4`为例：

### 安装Hadoop

1.安装依赖

* 安装Java 8

```bash
$ apt install -y openjdk-8-jdk
```

* 安装ssh和管理工具pdsh

```bash
$ apt install -y ssh pdsh
```

2.下载Hadoop

```bash
$ wget https://dlcdn.apache.org/hadoop/common/hadoop-3.3.4/hadoop-3.3.4.tar.gz
$ tar -zxf hadoop-3.3.4.tar.gz
$ cd hadoop-3.3.4/
```

3.修改环境变量文件`etc/hadoop/hadoop-env.sh`

```bash
# 设置JAVA_HOME
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

### 配置HDFS

1.修改`etc/hadoop/core-site.xml`:

```xml
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```

2.修改`etc/hadoop/hdfs-site.xml`:

```xml
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
</configuration>
```

3.设置无密码登录ssh

```bash
$ ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
$ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

4.格式化文件系统

```bash
$ bin/hdfs namenode -format
```

5.启动NameNode和DataNode

```bash
$ sbin/start-dfs.sh
Starting namenodes on [localhost]
ERROR: Attempting to operate on hdfs namenode as root
ERROR: but there is no HDFS_NAMENODE_USER defined. Aborting operation.
Starting datanodes
ERROR: Attempting to operate on hdfs datanode as root
ERROR: but there is no HDFS_DATANODE_USER defined. Aborting operation.
Starting secondary namenodes [xdata4]
ERROR: Attempting to operate on hdfs secondarynamenode as root
ERROR: but there is no HDFS_SECONDARYNAMENODE_USER defined. Aborting operation.
```

根据提示信息，设置环境变量：

```bash
export HDFS_NAMENODE_USER=root
export HDFS_DATANODE_USER=root
export HDFS_SECONDARYNAMENODE_USER=root
```

```bash
$ sbin/start-dfs.sh 
Starting namenodes on [localhost]
pdsh@hgs: localhost: connect: Connection refused
Starting datanodes
pdsh@hgs: localhost: connect: Connection refused
Starting secondary namenodes [hgs]
pdsh@hgs: hgs: connect: Connection refused
```

pdsh默认采用rsh协议进行登录，改用ssh协议即可：

```bash
export PDSH_RCMD_TYPE=ssh
```

```bash
$ sbin/start-dfs.sh
Starting namenodes on [localhost]
Starting datanodes
Starting secondary namenodes [hgs]
hgs: Warning: Permanently added 'hgs,172.16.2.24' (ECDSA) to the list of known hosts.
```

### 执行测试程序

1.创建目录：

```bash
$ bin/hdfs dfs -mkdir /user
# bin/hdfs dfs -mkdir /user/<username>，username为环境变量中的用户名
$ bin/hdfs dfs -mkdir /user/root
```

2.将输入文件拷贝到分布式文件系统中

```bash
$ bin/hdfs dfs -mkdir input
$ bin/hdfs dfs -put etc/hadoop/*.xml input
```

3.运行示例文件

```bash
$ bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.4.jar grep input output 'dfs[a-z.]+'
```

4.将输出文件拷贝到本地

```bash
$ bin/hdfs dfs -get output output
$ cat output/*
1	dfsadmin
1	dfs.replication
```

检查分布式文件系统中的输出结果：

```bash
$ bin/hdfs dfs -cat output/*
1	dfsadmin
1	dfs.replication
```

5.删除输出结果

```bash
$ bin/hdfs dfs -rm -r -f ouput
Deleted output
```

### 配置YARN

1.修改配置文件

配置`etc/hadoop/mapred-site.xml`:

```xml
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
    <property>
        <name>mapreduce.application.classpath</name>
        <value>$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*:$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib/*</value>
    </property>
</configuration>
```

配置`etc/hadoop/yarn-site.xml`:

```xml
<configuration>
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
    <property>
        <name>yarn.nodemanager.env-whitelist</name>
        <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_HOME,PATH,LANG,TZ,HADOOP_MAPRED_HOME</value>
    </property>
</configuration>
```

2.启动YARN（资源管理器和节点管理器）

```bash
$ sbin/start-yarn.sh
Starting resourcemanager
ERROR: Attempting to operate on yarn resourcemanager as root
ERROR: but there is no YARN_RESOURCEMANAGER_USER defined. Aborting operation.
Starting nodemanagers
ERROR: Attempting to operate on yarn nodemanager as root
ERROR: but there is no YARN_NODEMANAGER_USER defined. Aborting operation.
```

根据错误提示，设置环境变量：

```bash
export YARN_RESOURCEMANAGER_USER=root
export YARN_NODEMANAGER_USER=root
```

```bash
$ sbin/start-yarn.sh
Starting resourcemanager
Starting nodemanagers
```

3.重新执行测试程序

```bash
$ bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.4.jar grep input output 'dfs[a-z.]+'
$ bin/hdfs dfs -cat output/*
1	dfsadmin
1	dfs.replication
```

### 停止Hadoop

1.删除input和output

```bash
$ bin/hdfs dfs -rm -r -f input output
Deleted input
Deleted output
```

2.停止YARN和HDFS

```bash
$ sbin/stop-yarn.sh
Stopping nodemanagers
localhost: WARNING: nodemanager did not stop gracefully after 5 seconds: Trying to kill with kill -9
Stopping resourcemanager
$ sbin/stop-dfs.sh
Stopping namenodes on [localhost]
Stopping datanodes
Stopping secondary namenodes [hgs]
```

### 参考文献

1. Hadoop: Setting up a Single Node Cluster, https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html
