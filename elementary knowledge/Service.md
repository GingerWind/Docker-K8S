通过service 访问pod

```
apiVersion: v1
kind: Service
metadata:
  name: httpd-svc
spec:
  type: NodePort
  selector:
    env: dev
    project: cluster
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 80
      
```



service 的apiVersion 为v1

​                 kind 为 Service



selector  挑选标签为dev环境的cluster的project

ports   将pod的80端口映射到service的8080端口，使用tcp协议



type:   ClusterIP    Cluster内的节点和pod访问

​			NodePort   对外提供服务

​			LoadBalancer    利用cloud provider 对外提供服务。目前支持的cloud provider有GCP、AWS、Azur

​            