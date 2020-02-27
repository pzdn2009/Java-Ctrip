# gitbook

```text
Source files: **/*.*

Exec command:
kill -9 `ps aux|grep gitbook|awk '{print $2}'|sed -n 1p`
nohup /opt/node-v4.4.7-linux-x64/bin/gitbook serve &
```

