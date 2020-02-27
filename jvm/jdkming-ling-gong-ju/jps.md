# jps

jps是JVM Process Status Tool的简称，用于显示指定系统内所有的HotSpot虚拟机进程。

```text
jps -l #显示主类全名
jps -v #显示JVM参数
```

第一列为：Local Virtual Machine Identifier,LVMID——虚拟机进程ID。

命令格式：

```text
 jps [options] [hostid]
```

* options: -vml
* hostid: hostname:port

