# jenkins发布jar

1. 执行命令打包

   ```text
   mvn clean package -Dmaven.test.skip=true
   ```

2. Execute shell script on remote host using ssh：

   ```text
   supervisorctl stop union-ms
   ```

3. Send files or execute cmd over SSH：

   ```text
   Source files    :**/union*.jar
   Remove prefix    :target
   Remote directory:union-ms
   ```

4. Execute shell script on remote host using ssh：

   ```text
   supervisorctl start union-ms
   ```

