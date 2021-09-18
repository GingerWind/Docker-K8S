1.通过deployment运行应用

kubectl create deployment  nginx-deployment --image=nginx:1.7.9 --replicas=2 -n test



2.
```linux
apiVersion: extensions/v1beta1

kind: Deployment

metadata:

  name:  nginx-deployment

spec:

  replicas: 1

  template:

​    labels:

​      app: web_server

​    spec:

​      containers:

​      -  name: nginx

​         image: nginx:1.7.9



apiVersion   当前配置格式的版本

kind    要创建的资源类型

metadata    资源的元数据，name是必须元数据项

spec    规格说明

  replicas      副本数量，默认是1

  template    定义pod的模版，重要部分。

    metadata   定义pod的元数据，至少要定义一个label

​    spec          描述pod的规格，name和image是必须的





Deployment 部署的副本 Pod 会分布在各个 Node 上，每个 Node 都可能运行好几个副本。DaemonSet 的不同之处在于：每个 Node 上最多只能运行一个副本




```