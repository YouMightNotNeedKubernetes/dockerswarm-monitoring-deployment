## Troubleshoot

### List all nodes and their labels

```sh
$ docker node ls -q | xargs docker node inspect -f '{{ .ID }}    {{ .Description.Hostname }}    {{ range $k, $v := .Spec.Labels }}{{ $k }}={{ $v }} {{end}}'

2pand    monitoring-manager-1   alertmanager=true consul=true etcd=true
cxz3g    monitoring-manager-2   alertmanager=true consul=true etcd=true prometheus=true
p8leg    monitoring-manager-3   alertmanager=true consul=true etcd=true prometheus=true
wd0l0    monitoring-worker-1    grafana-loki=true grafana-mimir=true
tex5p    monitoring-worker-2    grafana-loki=true grafana-mimir=true
ne00k    monitoring-worker-3    grafana-loki=true grafana-mimir=true
lfnjd    monitoring-worker-4    minio=true postgres=true
molsl    monitoring-worker-5    minio=true postgres=true
4ljrz    monitoring-worker-6    minio=true postgres=true
pogcq    monitoring-worker-7    grafana=true
```

### Remove all labels from all nodes

```
$ docker node ls -q | xargs docker node inspect -f 'docker node update {{ range $k, $v := .Spec.Labels }} --label-rm {{ $k }}{{end}} {{.Description.Hostname}}' | bash
```
