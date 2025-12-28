# Monitoring infrastructure for AI_INFN

AI_INFN operates a Kubernetes cluster with GPUs for interactive and batch workloads.

This repository provides a recipe for a monitoring infrastructure similar to that used in production for AI_INFN.
The main differences with version running in production is that this one is decoupled from the other services, 
while in production there are additional sensors and scrapers for dedicated use cases. 
In the future we will migrate the AI_INFN production monitoring to this more modular setup, moving sensors and scrapers 
closer to the relevant services.

The structure of the monitoring is as follows.

 * Prometheus-stack is installed with Helm-Controller and is used to configure the prometheus database, but not grafana;
 * Kube-eagle is installed with Helm-Controller and provides standard exporters;
 * Grafana is set up independently, in the same pod with an ephemeral Postgres database used to collect metrics that do not fit the prometheus pull approach;
 * A custom exporter is deployed to keep track of the special resources used in multiple namespaces, by querying the Kubernetes API server.

## Extending with Prometheus ServiceMonitor
In most circumstances, Prometheus is enough to keep track of what happens in the cluster. 
You should use a define an exporter and then a ServiceMonitor resource to make it discoverable by Prometheus. 

Remember that:
 * after 15 days or upon service restart, the metrics stored in prometheus are lost. DO NOT USE FOR ACCOUNTING.

## Extending with Postgres injectors
For complicated metrics, you may need to create tables and structures in the ephemeral postrgres database. 

Remember that,
 * upon service restart, the metrics stored in postgres are lost. DO NOT USE FOR ACCOUNTING.
 * if the ephemeral storage used by postgres exceeds its limit, the service is restarted and the storage it gets cleared-up. 


