Master

  api server

  HTTP/HTTPS RESTful API,客户端工具通过api管理cluster的资源

  scheduler

  决定pod运行的node节点

  controller manager

  管理cluster资源。包括replication controller,endpoint controller, namespace controller,    serviceaccounts controller。

  etcd

  保存信息（配置信息、资源信息）

  pod网络

Node

  kubelet

  agent。

  kube-proxy

  转发service收到的转发到pod的请求

   pod网络



查看所有支持api版本

 ```
kubectl api-versions
 ```









