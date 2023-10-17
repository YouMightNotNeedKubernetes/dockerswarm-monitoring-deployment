## Troubleshoot

### List all nodes and their labels

```sh
$ docker node ls -q | xargs docker node inspect -f '{{ .ID }}    {{ .Description.Hostname }}    {{ range $k, $v := .Spec.Labels }}{{ $k }}={{ $v }} {{end}}'

# 2pa    monitoring-manager-1    consul=true etcd=true postgres=true
# cxz    monitoring-manager-2    consul=true etcd=true prometheus=true
# p8l    monitoring-manager-3    consul=true etcd=true prometheus=true
# wd0    monitoring-worker-1     alertmanager=true loki=true
# tex    monitoring-worker-2     alertmanager=true loki=true
# ne0    monitoring-worker-3     alertmanager=true loki=true
# lfn    monitoring-worker-4     mimir=true minio=true
# mol    monitoring-worker-5     mimir=true minio=true
# 4lj    monitoring-worker-6     minio=true postgres=true
# pog    monitoring-worker-7     minio=true postgres=true
```

### Remove all labels from all nodes

```
docker node ls -q | xargs docker node inspect -f 'docker node update {{ range $k, $v := .Spec.Labels }} --label-rm {{ $k }}{{end}} {{.Description.Hostname}}' | bash
```
