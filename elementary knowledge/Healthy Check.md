liveness

什么时候通过重启容器恢复

```
livenessProbe:
  exec:
    command:
    - cat
    - /tmp/healthy
  initialDelaySeconds: 10
  periodSeconds: 5
```

容器启动 10 秒之后开始执行 Liveness 探测，我们一般会根据应用启动的准备时间来设置

每 5 秒执行一次 Liveness 探测 。Kubernetes 如果连续执行 3 次 Liveness 探测均失败，则会杀掉并重启容器。



readiness

什么时候将容器加入Service中对外提供服务

```
readinessProbe:
  exec:
    command:
    - cat
    - /tmp/healthy
  initialDelaySeconds: 10
  periodSeconds: 5
```



Scale Up

```
readinessProbe:
  httpGet:
    scheme: HTTP
    path: /healthy
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 5
```

schema 指定协议，支持 HTTP（默认值）和 HTTPS。
path 指定访问路径。
port 指定端口。



Rolling Update

```
  strategy:
    rollingUpdate:
      maxSurge: 35%
      maxUnavailable: 35%
```

maxSurge 值越大，初始创建的新副本数量就越多；

maxUnavailable 值越大，初始销毁的旧副本数量就越多。







相同：通过容器启动进程的返回值是否为零来判断探测是否成功

不同：liveness 重启容器

​            readiness  将容器设置为不可用，不接受Service转发的请求



相互独立，可单独使用或同时使用

