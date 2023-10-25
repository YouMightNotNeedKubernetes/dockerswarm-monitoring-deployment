## Environment variables

The following environment variables are available for configure the Monitoring Stack:

```env
# ===============================================================
# Grafana
# See https://github.com/grafana/grafana/releases
# ===============================================================
export GRAFANA_VERSION=latest
# export GRAFANA_REPLICAS=3
# export GRAFANA_PORT=3000

# ===============================================================
# Grafana Loki
# See https://github.com/grafana/loki/releases
# ===============================================================
export GRAFANA_LOKI_VERSION=latest
# export GRAFANA_LOKI_REPLICAS=3
# export GRAFANA_LOKI_PORT=3100

# Promtail
# Please set PROMTAIL_VERSION to the same version as GRAFANA_LOKI_VERSION
export PROMTAIL_VERSION=latest
# export PROMTAIL_PORT=9080

# ===============================================================
# Grafana Mimir
# See https://github.com/grafana/mimir/releases
# ===============================================================
export GRAFANA_MIMIR_VERSION=latest
# export GRAFANA_MIMIR_REPLICAS=3
# export GRAFANA_MIMIR_PORT=3200

# ===============================================================
# Prometheus
# See https://github.com/prometheus/prometheus/releases
# ===============================================================
export PROMETHEUS_VERSION=latest
# export PROMETHEUS_REPLICAS=2
# export PROMETHEUS_PORT=9090

# Alertmanager
# See https://github.com/prometheus/alertmanager/releases
export ALERTMANAGER_VERSION=latest
# export ALERTMANAGER_REPLICAS=3
# export ALERTMANAGER_PORT=9093

# Promagent
# See https://github.com/prometheus/node_exporter/releases
export NODE_EXPORTER_VERSION=latest

# The "latest" tag no longer works for "gcr.io/cadvisor/cadvisor"
# You must hardcode it to a newer version like so: gcr.io/cadvisor/cadvisor:v0.47.2
# https://github.com/google/cadvisor/issues/3023#issuecomment-1526194006
# 
# See https://github.com/google/cadvisor/releases
export CADVISOR_VERSION=v0.47.2
```

And the following for the optional stacks such as **MinIO**, **PostgreSQL** and others...
```env
# ===============================================================
# ETCD
# ===============================================================
export ETCD_VERSION=v3.5.9
# export ETCD_REPLICAS=3

# ===============================================================
# Hashicorp Consul
# ===============================================================
export CONSUL_VERSION=latest
# export CONSUL_REPLICAS=3

# ===============================================================
# Minio
# ===============================================================
export MINIO_VERSION=latest
# export MINIO_REPLICAS=3
# export MINIO_PORT=9000
# export MINIO_CONSOLE_PORT=9001

# Minio Ingress
export MINIO_NGINX_VERSION=stable-alpine

# ===============================================================
# Postgres
# ===============================================================
export SPILO_PGSQL_VERSION=3.0-p1
# export SPILO_PGSQL_REPLICAS=3

# Postgres Loadbalancer
export SPILO_PGSQL_LB_VERSION=latest
# export SPILO_PGSQL_LB_REPLICAS=1
# export SPILO_PGSQL_LB_PRIMARY_PORT=5432
# export SPILO_PGSQL_LB_REPLICAS_PORT=5433
# export SPILO_PGSQL_LB_SPILO_PORT=5480
# export SPILO_PGSQL_LB_STATS_PORT=5484
```
