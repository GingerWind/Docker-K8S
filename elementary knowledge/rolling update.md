查看历史更新记录

```
kubectl rollout history statefulset dev-biz -n dev
```

statefulset/deployment/daemonset



回滚到某个版本

```
  kubectl rollout undo statefulset dev-biz --to-revision=1
```



