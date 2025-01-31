---
hide:
  - toc
---

# 升级到 v0.12.x

0.12.x 将 kube-prometheus-stack chart 从 39.6.0 升级到 41.9.1，其中包括 prometheus-operator 升级到 v0.60.1, prometheus-node-exporter chart 升级到 4.3.0 等。
 prometheus-node-exporter 升级后使用了 [Kubernetes 推荐 label](https://kubernetes.io/docs/concepts/overview/working-with-objects/common-labels/)，因此需要在升级前删除 `node-exporter` 的 daemonset。
 prometheus-operator 更新了 CRD，因此需要在升级 insight agent 前执行如下命令: 

```shell linenums="1"
kubectl delete daemonset insight-agent-prometheus-node-exporter -n insight-system
kubectl apply --server-side -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.60.1/example/prometheus-operator-crd/monitoring.coreos.com_alertmanagerconfigs.yaml --force-conflicts
kubectl apply --server-side -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.60.1/example/prometheus-operator-crd/monitoring.coreos.com_alertmanagers.yaml --force-conflicts
kubectl apply --server-side -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.60.1/example/prometheus-operator-crd/monitoring.coreos.com_podmonitors.yaml --force-conflicts
kubectl apply --server-side -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.60.1/example/prometheus-operator-crd/monitoring.coreos.com_probes.yaml --force-conflicts
kubectl apply --server-side -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.60.1/example/prometheus-operator-crd/monitoring.coreos.com_prometheuses.yaml --force-conflicts
kubectl apply --server-side -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.60.1/example/prometheus-operator-crd/monitoring.coreos.com_prometheusrules.yaml --force-conflicts
kubectl apply --server-side -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.60.1/example/prometheus-operator-crd/monitoring.coreos.com_servicemonitors.yaml --force-conflicts
kubectl apply --server-side -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.60.1/example/prometheus-operator-crd/monitoring.coreos.com_thanosrulers.yaml --force-conflicts
```

!!! note

    如您是离线安装，可以在解压 insight-agent 离线包后，执行以下命令更新 CRD。
    
    ```shell
    kubectl apply --server-side -f insight-agent/dependency-crds --force-conflicts
    ```
