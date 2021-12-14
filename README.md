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

Below is the output of the deployed chart:

```
[Saurabh MacBook-new] # helm test elasticsearch
NAME: elasticsearch
LAST DEPLOYED: Tue Dec 14 21:24:21 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE:     elasticsearch-xqmek-test
Last Started:   Tue Dec 14 21:28:07 2021
Last Completed: Tue Dec 14 21:28:10 2021
Phase:          Succeeded
NOTES:
1. Watch all cluster members come up.
  $ kubectl get pods --namespace=default -l app=elasticsearch-picnic-master -w
2. Retrieve elastic user's password.
  $ kubectl get secrets --namespace=default elasticsearch-picnic-master-credentials -ojsonpath='{.data.password}' | base64 -d
3. Test cluster health using Helm test.
  $ helm --namespace=default test elasticsearch
```

```
[Saurabh MacBook-new] # kubectl get all
NAME                                READY   STATUS    RESTARTS   AGE
pod/elasticsearch-picnic-master-0   1/1     Running   0          131m
pod/elasticsearch-picnic-master-1   1/1     Running   0          131m
pod/elasticsearch-picnic-master-2   1/1     Running   0          131m

NAME                                           TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE
service/elasticsearch-picnic-master            ClusterIP   172.20.161.109   <none>        9200/TCP,9300/TCP   131m
service/elasticsearch-picnic-master-headless   ClusterIP   None             <none>        9200/TCP,9300/TCP   131m
service/kubernetes                             ClusterIP   172.20.0.1       <none>        443/TCP             7d22h

NAME                                           READY   AGE
statefulset.apps/elasticsearch-picnic-master   3/3     131m
```

To access the ES cluster, elastic user password is required. You can use below command to retrieve the password:

```
[Saurabh MacBook-new] # kubectl get secrets --namespace=default elasticsearch-picnic-master-credentials -ojsonpath='{.data.password}' | base64 -d
```


## Configuration

All the configuration parameters to customized the Elastic Search cluster deployment can be found [here](https://github.com/elastic/helm-charts/tree/main/elasticsearch#configuration): 

| Parameter                          | Description                                                                                                                                                                                                                                                                                                       | Default                                          |
|------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------|
| `clusterName`                      | This will be used as the Elasticsearch [cluster.name][] and should be unique per cluster in the namespace                                                                                                                                                                                                         | `elasticsearch`                                  |
| `esConfig`                         | Allows you to add any config files in `/usr/share/elasticsearch/config/` such as `elasticsearch.yml` and `log4j2.properties`. See [values.yaml][] for an example of the formatting                                                                                                                                | `{}`                                             |
| `persistence`                      | Enables a persistent volume for Elasticsearch data. Can be disabled for nodes that only have [roles][] which don't require persistent data                                                                                                                                                                        | see [values.yaml][]                              |
| `volumeClaimTemplate`              | Configuration for the [volumeClaimTemplate for StatefulSets][]. You will want to adjust the storage (default `30Gi` ) and the `storageClassName` if you are using a different storage class                                                                                                                       | see [values.yaml][]                              |
