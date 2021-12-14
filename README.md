# Elasticsearch Cluster Helm Chart


This Helm chart is a way to configure and run Elastic Search Cluster. This would deploy a multi node ES cluster that would ensure availability if any K8s node fails. All the ES roles are assigned to all the nodes in the cluster. 

ES cluster requires Persistent Volume (PV) to store the Elasticsearch data. This solution has been tested with Amazon EKS cluster, this is the reason the storageclass is defined as `gp2`. 

This can be changed by updating the storageclass name in values.yaml file.
```
volumeClaimTemplate:
  accessModes: ["ReadWriteOnce"]
  storageClassName: "gp2"
  resources:
    requests:
      storage: 30Gi
```

We can also use Elastic Cloud on Kubernetes which is based on Operator pattern and is Elastic recommended way to deploy Elasticsearch. 

## Future Scope:

We can use continous delivery tool like ArgoCD or FluxCD to deploy the helm chart on the cluster. These tools are based on GitOps principles which will automatically apply the changes to the desired state.


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

You can now setup a port forward to query Elasticsearch API:

```
[Saurabh MacBook-new] # kubectl port-forward svc/elasticsearch-picnic-master 9200 &
[1] 16093
Forwarding from 127.0.0.1:9200 -> 9200
Forwarding from [::1]:9200 -> 9200
```

To gauge the cluster health & general info on the cluster you use below command:
```
[Saurabh MacBook-new] # curl -XGET 'localhost:9200/_cluster/health?pretty'
Handling connection for 9200
{
  "cluster_name" : "elasticsearch-picnic",
  "status" : "green",
  "timed_out" : false,
  "number_of_nodes" : 3,
  "number_of_data_nodes" : 3,
  "active_primary_shards" : 1,
  "active_shards" : 2,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 0,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 100.0
}
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
