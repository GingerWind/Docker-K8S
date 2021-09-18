用户名admin

密码 123456



创建secret

通过`--from-literal`

```
kubectl create secret generic mysecret --from-literal=username=admin --from-literal=password=123456
```

每个 `--from-literal` 对应一个信息条目



通过 `--from-file`：

```
echo -n admin > ./username
echo -n 123456 > ./password
kubectl create secret generic mysecret --from-file=./username --from-file=./password
```

每个文件内容对应一个信息条目。



通过 `--from-env-file`：

```
cat << EOF > env.txt
username=admin
password=123456
EOF
kubectl create secret generic mysecret --from-env-file=env.txt
```

文件 `env.txt` 中每行 Key=Value 对应一个信息条目。



通过yaml配置文件

```
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
data:
  username: YWRtaW4=
  password: MTIzNDU2
```

```
ld@lddeMacBook-Air elementary knowledge % echo -n admin | base64
YWRtaW4=
```

```
% kubectl edit secret mysecret -n test
% echo -n  MTIzNDU2 | base64 --decode
```



volume方式使用secret

```
spec:
  ...
    volumeMounts:
    - name: foo
      mountPath: "/etc/foo"
      readOnly: true
  volumes:
  - name: foo
    secret:
      secretName: mysecret
      items:
      - key: username
        path: my-group/my-username
      - key: password
        path: my-group/my-password
```



通过环境变量方式使用secret

```
spec:
  ...
    env:
      - name: SECRET_USERNAME
        valueFrom:
          secretKeyRef:
            name: mysecret
            key: username
      - name: SECRET_PASSWORD
        valueFrom:
          secretKeyRef:
            name: mysecret
            key: password
```



非敏感数据用configmap

```
  containers:
  - env:
    - name: NACOS_REPLICAS
      value: "3"
    - name: MYSQL_SERVICE_HOST
      valueFrom:
        configMapKeyRef:
          key: mysql.host
          name: nacos-cm
    - name: MYSQL_SERVICE_DB_NAME
      valueFrom:
        configMapKeyRef:
          key: mysql.db.name
          name: nacos-cm
    - name: MYSQL_SERVICE_PORT
      valueFrom:
        configMapKeyRef:
          key: mysql.port
          name: nacos-cm
    - name: MYSQL_SERVICE_USER
      valueFrom:
        configMapKeyRef:
          key: mysql.user
          name: nacos-cm
    - name: MYSQL_SERVICE_PASSWORD
      valueFrom:
        configMapKeyRef:
          key: mysql.password
          name: nacos-cm
    - name: NACOS_SERVER_PORT
      value: "8848"
    - name: NACOS_APPLICATION_PORT
      value: "8848"
    - name: PREFER_HOST_MODE
      value: hostname
    - name: NACOS_SERVERS
      value: nacos-0.nacos-headless.dev.svc.cluster.local:8848 nacos-1.nacos-headless.dev.svc.cluster.local:8848
        nacos-2.nacos-headless.dev.svc.cluster.local:8848
```

```
ld@lddeMacBook-Air elementary knowledge % kubectl describe configmap nacos-cm -n dev
Name:         nacos-cm
Namespace:    dev
Labels:       <none>
Annotations:  <none>

Data
====
mysql.db.name:
----
nacos
mysql.host:
----
xxx
mysql.password:
----
xxx
mysql.port:
----
3306
mysql.user:
----
dev_cluster
Events:  <none>
```



![image-20210915104301635](/Users/ld/Library/Application Support/typora-user-images/image-20210915104301635.png)



