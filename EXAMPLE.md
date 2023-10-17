
## Here an example deployment

This is an example of a 10 nodes cluster with 3 managers and 7 workers and their associated labels.

```sh
$ docker node ls

# ID         HOSTNAME               STATUS    AVAILABILITY  MANAGER STATUS   ENGINE VERSION  Labels
# 2pan   *   monitoring-manager-1   Ready     Active        Leader           24.0.6          etcd=true,    consul=true,    alertmanager=true
# cxz3       monitoring-manager-2   Ready     Active        Reachable        24.0.6          etcd=true,    consul=true,    prometheus=true, alertmanager=true
# p8le       monitoring-manager-3   Ready     Active        Reachable        24.0.6          etcd=true,    consul=true,    prometheus=true, alertmanager=true
# wd0l       monitoring-worker-1    Ready     Active                         24.0.6          loki=true,    mimir=true
# tex5       monitoring-worker-2    Ready     Active                         24.0.6          loki=true,    mimir=true
# ne00       monitoring-worker-3    Ready     Active                         24.0.6          loki=true,    mimir=true
# lfnj       monitoring-worker-4    Ready     Active                         24.0.6          minio=true,   postgres=true
# mols       monitoring-worker-5    Ready     Active                         24.0.6          minio=true,   postgres=true
# 4ljr       monitoring-worker-6    Ready     Active                         24.0.6          minio=true,   postgres=true
# pogc       monitoring-worker-7    Ready     Active                         24.0.6          grafana=true
```

Add labels to the nodes.

```sh
docker node update --label-add etcd=true --label-add consul=true --label-add alertmanager=true monitoring-manager-1
docker node update --label-add etcd=true --label-add consul=true --label-add prometheus=true --label-add alertmanager=true monitoring-manager-2
docker node update --label-add etcd=true --label-add consul=true --label-add prometheus=true --label-add alertmanager=true monitoring-manager-3
docker node update --label-add loki=true --label-add mimir=true monitoring-worker-1
docker node update --label-add loki=true --label-add mimir=true monitoring-worker-2
docker node update --label-add loki=true --label-add mimir=true monitoring-worker-3
docker node update --label-add minio=true --label-add postgres=true monitoring-worker-4
docker node update --label-add minio=true --label-add postgres=true monitoring-worker-5
docker node update --label-add minio=true --label-add postgres=true monitoring-worker-6
docker node update --label-add grafana=true monitoring-worker-7
```
