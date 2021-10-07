# 实践：CPU 使用率过高

## 步骤

* 使用jps获得进程id
* 使用top命令获取占用CPU最高的线程
* 将线程ID转为16进制
* 在jstack中搜索对应的线程ID
* 查看并分析代码

```text
//1. 
jps 
top -H -p pid

//2.
printf "%x\n" pid
jstack pid | grep 0xtid -A 30
# -l  long listing. Prints additional information about locks
jstack -l pid >> /temp/pid.dump 


//3. view code

```



