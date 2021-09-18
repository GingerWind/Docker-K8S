服务类容器通常持续提供服务，需要一直运行，比如 http server，daemon 等。工作类容器则是一次性任务，比如批处理程序，完成后容器就退出。

Kubernetes 的 Deployment、ReplicaSet 和 DaemonSet 都用于管理服务类容器；对于工作类容器，我们用 Job。



批处理程序，每个副本（Pod）都会从任务池中读取任务并执行，副本越多，执行时间就越短，效率就越高。这种类似的场景都可以用 Job 来实现。



pod数量为2

```
apiVersion: batch/v1
kind: Job
metadata: 
  name: myjob
spec:
  parallelism: 2
  template:
    metadata:
      name: myjob
    spec:
      containers:
      - name: hello
        image: busybox
        command: ['echo','hello']
      restartPolicy: OnFailure
```



定时任务

```
apiVersion: batch/v2alpha1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox
            command: ['echo','hello']
          restartPolicy: OnFailure
```



