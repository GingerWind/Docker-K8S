## Containerd 和 Docker

- containerd调用链更短，组建更少，更稳定，占用节点资源更少

- 以下情况用docker

  docker in docker

  docker build/push/save/load

  docker API

  docker compose



##### 调用链区别有哪些？

- Docker 作为 K8S 容器运行时，调用关系如下：
  `kubelet --> docker shim （在 kubelet 进程中） --> dockerd --> containerd`
- Containerd 作为 K8S 容器运行时，调用关系如下：
  `kubelet --> cri plugin（在 containerd 进程中） --> containerd`



##### stream 服务

- Docker API 本身提供stream服务

- containerd的stream服务需要单独配置

  ```
  [plugins.cri]
    stream_server_address = "127.0.0.1"
    stream_server_port = "0"
    enable_tls_streaming = false
  ```

  

##### 其他

| 对比项                 | Docker                                                       | Containerd                                                   |
| :--------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| 存储路径               | 如果 Docker 作为 K8S 容器运行时，容器日志的落盘将由 docker 来完成，保存在类似`/var/lib/docker/containers/$CONTAINERID` 目录下。Kubelet 会在 `/var/log/pods` 和 `/var/log/containers` 下面建立软链接，指向 `/var/lib/docker/containers/$CONTAINERID` 该目录下的容器日志文件。 | 如果 Containerd 作为 K8S 容器运行时， 容器日志的落盘由 Kubelet 来完成，保存至 `/var/log/pods/$CONTAINER_NAME` 目录下，同时在 `/var/log/containers` 目录下创建软链接，指向日志文件。 |
| 配置参数               | 在 docker 配置文件中指定： `"log-driver": "json-file",` `"log-opts": {"max-size": "100m","max-file": "5"}` | 方法一：在 kubelet 参数中指定： `--container-log-max-files=5--container-log-max-size="100Mi"` 方法二：在 KubeletConfiguration 中指定： `"containerLogMaxSize": "100Mi",` `"containerLogMaxFiles": 5,` |
| 把容器日志保存到数据盘 | 把数据盘挂载到 “data-root”（缺省是 `/var/lib/docker`）即可。 | 创建一个软链接 `/var/log/pods` 指向数据盘挂载点下的某个目录。 在 TKE 中选择“将容器和镜像存储在数据盘”，会自动创建软链接 `/var/log/pods`。 |



##### CNI网络

| 对比项         | Docker                                           | Containerd                                                   |
| :------------- | :----------------------------------------------- | :----------------------------------------------------------- |
| 谁负责调用 CNI | Kubelet 内部的 docker-shim                       | Containerd 内置的 cri-plugin（containerd 1.1 以后）          |
| 如何配置 CNI   | Kubelet 参数 `--cni-bin-dir` 和 `--cni-conf-dir` | Containerd 配置文件（toml）： `[plugins.cri.cni]` `bin_dir = "/opt/cni/bin"` `conf_dir = "/etc/cni/net.d"` |

