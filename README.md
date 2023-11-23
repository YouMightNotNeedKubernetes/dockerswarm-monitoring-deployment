> [!WARNING]
> This documentation and stacks are currently a work-in-progress.

# Docker Swarm Monitoring Guide
A documentation on how to get started with Docker Swarm Monitoring

## Stacks
- [grafana-loki](https://github.com/YouMightNotNeedKubernetes/grafana-loki): A high-availability Grafana Loki deployment for Docker Swarm.
- [grafana](https://github.com/YouMightNotNeedKubernetes/grafana): Docker Stack deployment for Grafana's Dashboard.
- [grafana-tempo](https://github.com/YouMightNotNeedKubernetes/grafana-tempo): A high-availability Grafana Tempo deployment for Docker Swarm.
- [grafana-mimir](https://github.com/YouMightNotNeedKubernetes/grafana-mimir): A high-availability Grafana Mimir deployment for Docker Swarm.
- [promtail](https://github.com/YouMightNotNeedKubernetes/promtail): Docker Stack deployment for Grafana Loki's Promtail.
- [prometheus](https://github.com/YouMightNotNeedKubernetes/prometheus): A high-availability prometheus stack for Docker Swarm.
- [alertmanager](https://github.com/YouMightNotNeedKubernetes/alertmanager): AboutA high-availability alertmanager stack for Docker Swarm.
- [promagents](https://github.com/YouMightNotNeedKubernetes/promagents): Docker Stack deployment for cAdvisor & node-exporter.

## Architecture Overview

![Architecture Overview](https://github.com/YouMightNotNeedKubernetes/dockerswarm-monitoring-for-scale-guide/assets/4363857/116c3b46-1988-4840-8537-cc438f1d2326)

## Prerequisites

- A Docker Swarm cluster with at least 3 managers and 7 workers.
- Object Storage (MinIO or Amazon S3)
- Database (PostgreSQL)

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

To add node labels, run the following command:

```sh
$ docker node update --label-add <key>=<value> <node-id>
```

For example, to add consul=true label to the manager-1 node:

```sh
$ docker node update --label-add consul=true monitoring-manager-1
$ docker node update --label-add consul=true monitoring-manager-2
$ docker node update --label-add consul=true monitoring-manager-3
```

> [!NOTE]
> Repeat the same process for other labels.
> See **Server placement** on each stack you planned to deploy.

## Getting Started

You might need to create swarm-scoped overlay network called dockerswarm_monitoring for all the stacks to communicate if you haven't already.

On the manager node, run the following command:

```sh
$ docker network create --scope swarm --driver overlay --attachable dockerswarm_monitoring
```

## Deploy promtail and promagents

This will distribute promtail and promagents to all the available nodes

There is no configuration needed for promtail and promagents, they will automatically discover the nodes and start scraping the metrics and logs.

**promtail**:

```sh
# https://github.com/YouMightNotNeedKubernetes/promtail
$ gh repo clone YouMightNotNeedKubernetes/promtail
$ cd promtail
$ make deploy
```

**promagents**:

```sh
# https://github.com/YouMightNotNeedKubernetes/promagents
$ gh repo clone YouMightNotNeedKubernetes/promagents
$ cd promagents
$ make deploy
```

## Deploy prometheus and alertmanager

The prometheus and alertmanager will be deployed as shown in this diagram.

![prometheus and alertmanager](https://user-images.githubusercontent.com/4363857/272507328-3a3e407e-d95a-4cad-afe6-f0259803943d.png)

### Deploy alertmanager

By default, it will deploy 3 replicas of Alertmanager. Having more than 3 replicas is way too much for a small cluster.

```sh
# https://github.com/YouMightNotNeedKubernetes/alertmanager
$ gh repo clone YouMightNotNeedKubernetes/alertmanager
$ cd alertmanager
$ make deploy
```

### Deploy prometheus

By default, it will deploy 2 replicas of Prometheus. Having more than 2 replicas is way too much for a small cluster.

```sh
# https://github.com/YouMightNotNeedKubernetes/prometheus
$ gh repo clone YouMightNotNeedKubernetes/prometheus
$ cd prometheus
$ make deploy
```

## Deploy Object Storage and Database

### MinIO

> [!NOTE]
> The MinIO Object Storage required for `grafana-mimir` and `grafana-loki` and need to be deployed first. If you planned to use Amazon S3, you can skip this stack.

See https://github.com/YouMightNotNeedKubernetes/minio for how to deploy MinIO.

### PostgreSQL

> [!NOTE]
> The PostgreSQL cluster using Spilo required `etcd` to be deployed first. If you planned to use alternative solution, you can skip this stack.

See https://github.com/YouMightNotNeedKubernetes/etcd for how to deploy etcd.

See https://github.com/YouMightNotNeedKubernetes/postgresql-spilo for how to deploy PostgreSQL.

## Deploy HashiCorp Consul

> [!IMPORTANT]
> HashiCorp Consul is required for `grafana-mimir` and `grafana-loki` stack. For deploying a high-availability cluster.

```sh
# https://github.com/YouMightNotNeedKubernetes/hashicorp-consul
$ gh repo clone YouMightNotNeedKubernetes/hashicorp-consul
$ cd hashicorp-consul
$ make deploy
```

See https://github.com/YouMightNotNeedKubernetes/hashicorp-consul for how to deploy HashiCorp Consul.

## Deploy Grafana Loki

```sh
# https://github.com/YouMightNotNeedKubernetes/grafana-loki
$ gh repo clone YouMightNotNeedKubernetes/grafana-loki
$ cd grafana-loki
$ make deploy
```

### Object Storage buckets
You will need to create bucket on MinIO or Amazon S3 for storing the logs.

- `loki`

> [!NOTE]
> You can change the bucket name using the configuration file.

See https://github.com/YouMightNotNeedKubernetes/grafana-loki for how to configure Grafana Loki.

## Deploy Grafana Mimir

```sh
# https://github.com/YouMightNotNeedKubernetes/grafana-mimir
$ gh repo clone YouMightNotNeedKubernetes/grafana-mimir
$ cd grafana-mimir
$ make deploy
```

### Object Storage buckets
You will need to create bucket on on MinIO or Amazon S3 for storing the metrics.

- `mimir`
- `mimir-blocks`
- `mimir-ruler`
- `mimir-alertmanager`

> [!NOTE]
> You can change the bucket name using the configuration file.

See https://github.com/YouMightNotNeedKubernetes/grafana-mimir for how to configure Grafana Mimir.

## Deploy Grafana Dashboard

This is a generic Grafana Dashboard, by default an embeded `sqlite` will be used as the default database. To achive a high-availability Grafana Dashboard, you will need to use external database such as **PostgreSQL** or **MySQL**.

```sh
# https://github.com/YouMightNotNeedKubernetes/grafana
$ gh repo clone YouMightNotNeedKubernetes/grafana
$ cd grafana
$ make deploy
```

See https://github.com/YouMightNotNeedKubernetes/grafana for how to configure Grafana Dashboard.
