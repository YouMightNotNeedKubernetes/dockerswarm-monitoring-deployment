> [!WARNING]
> This documentation and stacks are currently a work-in-progress.


# Docker Swarm Monitoring Guide
A documentation on how to get started with Docker Swarm Monitoring

## Stacks
- [grafana](https://github.com/YouMightNotNeedKubernetes/grafana): Docker Stack deployment for Grafana's Dashboard.
- [mimir](https://github.com/YouMightNotNeedKubernetes/mimir): A high-availability Grafana Mimir deployment for Docker Swarm.
- [loki](https://github.com/YouMightNotNeedKubernetes/loki): A high-availability Grafana Loki deployment for Docker Swarm.
- [promstack](https://github.com/YouMightNotNeedKubernetes/promstack): A high-availability prometheus/alertmanager stack for Docker Swarm.
- [promagents](https://github.com/YouMightNotNeedKubernetes/promagents): Docker Stack deployment for cAdvisor & node-exporter.
- [promtail](https://github.com/YouMightNotNeedKubernetes/promtail): Docker Stack deployment for Grafana Loki's Promtail.

## Architecture Overview

![Architecture Overview](https://github.com/YouMightNotNeedKubernetes/dockerswarm-monitoring-guide/assets/4363857/73cc89b6-6858-49cc-9f4d-f7da11a3a48d)

## Getting Started

You will need to create swarm-scoped overlay network called `dockerswarm_monitoring` for all the stacks to communicate.

```sh
$ docker network create --driver overlay --attachable dockerswarm_monitoring
```
