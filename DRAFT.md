> [!WARNING]
> This documentation and stacks are currently a work-in-progress.


# Docker Swarm Monitoring Guide
A documentation on how to get started with Docker Swarm Monitoring

## Stacks
- [grafana](https://github.com/YouMightNotNeedKubernetes/grafana): Docker Stack deployment for Grafana's Dashboard.
- [grafana-mimir](https://github.com/YouMightNotNeedKubernetes/grafana-mimir): A high-availability Grafana Mimir deployment for Docker Swarm.
- [grafana-loki](https://github.com/YouMightNotNeedKubernetes/grafana-loki): A high-availability Grafana Loki deployment for Docker Swarm.
- [promtail](https://github.com/YouMightNotNeedKubernetes/promtail): Docker Stack deployment for Grafana Loki's Promtail.
- [prometheus](https://github.com/YouMightNotNeedKubernetes/prometheus): A high-availability prometheus stack for Docker Swarm.
- [alertmanager](https://github.com/YouMightNotNeedKubernetes/alertmanager): AboutA high-availability alertmanager stack for Docker Swarm.
- [promagents](https://github.com/YouMightNotNeedKubernetes/promagents): Docker Stack deployment for cAdvisor & node-exporter.

**Additional Stacks**
- [etcd](https://github.com/YouMightNotNeedKubernetes/etcd): A high-availability Etcd deployment for Docker Swarm
- [postgresql-spilo](https://github.com/YouMightNotNeedKubernetes/postgresql-spilo): Postgres High Availability with patroni/spilo for Docker Swarm
- [minio](https://github.com/YouMightNotNeedKubernetes/minio): Docker Stack deployment for MinIO Object Storage.

> [!NOTE]
> The PostgreSQL cluster using Spilo required `etcd` to be deployed first. If you planned to use alternative solution, you can skip this stack.

> [!NOTE]
> The MinIO Object Storage required for `grafana-mimir` and `grafana-loki` and need to be deployed first. If you planned to use Amazon S3, you can skip this stack.

## Architecture Overview

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://github.com/YouMightNotNeedKubernetes/dockerswarm-monitoring-guide/assets/4363857/507dc46d-0537-45d2-a01a-11c6f57485b2">
  <source media="(prefers-color-scheme: light)" srcset="https://github.com/YouMightNotNeedKubernetes/dockerswarm-monitoring-guide/assets/4363857/b645d44a-c93d-4764-a34f-87cbdb25671e">
  <img alt="Architecture Overview" src="https://github.com/YouMightNotNeedKubernetes/dockerswarm-monitoring-guide/assets/4363857/507dc46d-0537-45d2-a01a-11c6f57485b2">
</picture>

## Getting Started

You might need to create swarm-scoped overlay network called dockerswarm_monitoring for all the stacks to communicate if you haven't already.

On the manager node, run the following command:

```sh
$ docker network create --driver overlay --attachable dockerswarm_monitoring
```

## Prerequisites

- A Docker Swarm cluster with at least 3 managers and 7 workers.

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

### Deploy prometheus

By default, it will deploy 2 replicas of Prometheus. Having more than 2 replicas is way too much for a small cluster.

```sh
# https://github.com/YouMightNotNeedKubernetes/prometheus
$ gh repo clone YouMightNotNeedKubernetes/prometheus
$ cd prometheus
$ make deploy
```

### Deploy alertmanager

By default, it will deploy 3 replicas of Alertmanager. Having more than 3 replicas is way too much for a small cluster.

```sh
# https://github.com/YouMightNotNeedKubernetes/alertmanager
$ gh repo clone YouMightNotNeedKubernetes/alertmanager
$ cd alertmanager
$ make deploy
```
