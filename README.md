# Elasticsearch Cluster Helm Chart


This Helm chart is a way to configure and run Elastic Search Cluster. We can also use Elastic Cloud on Kubernetes which is based on Operator pattern and is Elastic recommended way to deploy Elasticsearch. 


- [Requirements](#requirements)
- [Installing](#installing)
  - [Install released version using Helm repository](#install-released-version-using-helm-repository)
- [Configuration](#configuration)
## Requirements

* Kubernetes >= 1.14
* [Helm][] >= 2.17.0
* Minimum cluster requirements include the following to run this chart with
default settings. All of these settings are configurable.
  * Three Kubernetes nodes to respect the default "hard" affinity settings
  * 1GB of RAM for the JVM heap

## Installing

### Install released version using Helm command:

* Install it:
  1. Clone this repository.
  2. cd elastic-search-cluster
  3. with Helm 3: `helm install elasticsearch-picnic .`

## Configuration

All the configuration parameters to customized the Elastic Search cluster deployment can be found here: https://github.com/elastic/helm-charts/tree/main/elasticsearch#configuration

| Parameter                          | Description                                                                                                                                                                                                                                                                                                       | Default                                          |
|------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------|
| `clusterName`                      | This will be used as the Elasticsearch [cluster.name][] and should be unique per cluster in the namespace                                                                                                                                                                                                         | `elasticsearch`                                  |
| `esConfig`                         | Allows you to add any config files in `/usr/share/elasticsearch/config/` such as `elasticsearch.yml` and `log4j2.properties`. See [values.yaml][] for an example of the formatting                                                                                                                                | `{}`                                             |
| `persistence`                      | Enables a persistent volume for Elasticsearch data. Can be disabled for nodes that only have [roles][] which don't require persistent data                                                                                                                                                                        | see [values.yaml][]                              |
| `volumeClaimTemplate`              | Configuration for the [volumeClaimTemplate for StatefulSets][]. You will want to adjust the storage (default `30Gi` ) and the `storageClassName` if you are using a different storage class                                                                                                                       | see [values.yaml][]                              |
