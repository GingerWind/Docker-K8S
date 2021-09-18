```
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: access-httpd
spec:
  podSelector:
    matchLabels:
      run: httpd
  ingress:
  - from:
    - podSelector:
        matchLabels:
          access: "true"
    ports:
    - protocol: TCP
      port: 80
```

① 定义将此 Network Policy 中的访问规则应用于 label 为 `run: httpd` 的 Pod

② `ingress` 中定义只有 label 为 `access: "true"` 的 Pod 才能访问应用。

③ 只能访问 `80` 端口。



