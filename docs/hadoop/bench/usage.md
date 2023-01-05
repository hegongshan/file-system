Hadoop Benchmarks

```bash
$ hadoop jar ${HADOOP_HOME}/share/hadoop/mapreduce/hadoop-mapreduce-client-jobclient-*-tests.jar
```

问题：执行上述命令后，可能会出现找不到junit的TestCase类：

```bash
java.lang.NoClassDefFoundError: junit/framework/TestCase
Caused by: java.lang.ClassNotFoundException: junit.framework.TestCase
```

解决办法：将`junit-*.jar`复制到目录`${HADOOP_HOME}/share/hadoop/common/lib/`中。例如：

```bash
$ cp ${HADOOP_HOME}/share/hadoop/tools/lib/junit-*.jar ${HADOOP_HOME}/share/hadoop/common/lib/
```