# Setting up Multi-Node Redis Cluster on Google Cloud Engine 

Redis is a powerful tool for data storage and caching. Redis Cluster extends the functionality by offering sharding and correlated performance benefits, linear scaling, and higher availability because of how Redis stores data. The data is automatically split among multiple nodes, which allows operations to continue, even when a subset of the nodes are experiencing failures or are unable to communicate with the rest of the cluster.

## Pre-requisite

- Installing [Google Cloud SDK](https://dl.google.com/dl/cloudsdk/channels/rapid/GoogleCloudSDKInstaller.exe)

```
gcloud init
```




```
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

```

```


 
 - Create 3 Node GKE Cluster
 
 ```
 gcloud container clusters create k8s-lab1 --disk-size 10 --zone asia-east1-a --machine-type n1-standard-2 --num-nodes 3 --scopes compute-rw
 ```
 
 
 ```
 k get nodes
NAME                                       STATUS   ROLES    AGE    VERSION
gke-cluster-1-default-pool-0de11b8d-077v   Ready    <none>   115m   v1.17.15-gke.800
gke-cluster-1-default-pool-0de11b8d-c0pk   Ready    <none>   116m   v1.17.15-gke.800
gke-cluster-1-default-pool-0de11b8d-nqlk   Ready    <none>   115m   v1.17.15-gke.800
```


## Cloning this Repo

```
$git clone https://github.com/willsc/Redis-deployment.git
cd redis/
```


```
$ kubectl apply -f redis-statefulset.yaml
configmap/redis-cluster created
statefulset.apps/redis-cluster created
```

```
$ kubectl apply -f redis-svc.yaml
service/redis-cluster created
```

```
k get po
NAME              READY   STATUS    RESTARTS   AGE
redis-cluster-0   1/1     Running   0          74m
redis-cluster-1   1/1     Running   0          74m
redis-cluster-2   1/1     Running   0          73m
redis-cluster-3   1/1     Running   0          73m
redis-cluster-4   1/1     Running   0          73m
redis-cluster-5   1/1     Running   0          72m
```

```
k get pvc
NAME                   STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-redis-cluster-0   Bound    pvc-f6e19b6a-982d-4469-87d7-332c5447436d   10Gi       RWO            standard       74m
data-redis-cluster-1   Bound    pvc-88e5b38b-009f-42a6-8d0c-02ffde085807   10Gi       RWO            standard       74m
data-redis-cluster-2   Bound    pvc-455b9237-da0d-4671-a4c0-e11e2b73d481   10Gi       RWO            standard       74m
data-redis-cluster-3   Bound    pvc-e1a4c3bc-3024-4b19-8b1d-87ef3d2d5699   10Gi       RWO            standard       73m
data-redis-cluster-4   Bound    pvc-9581b68e-846f-44dc-a5a2-39be903fb6c6   10Gi       RWO            standard       73m
data-redis-cluster-5   Bound    pvc-88d79f84-ee97-43b4-8dd3-ef2b42e71603   10Gi       RWO            standard       73m


```
```
k describe pods
Name:         redis-cluster-0
Namespace:    default
Priority:     0
Node:         gke-cluster-1-default-pool-0de11b8d-nqlk/10.154.15.198
Start Time:   Wed, 17 Feb 2021 21:13:03 +0000
Labels:       app=redis-cluster
              controller-revision-hash=redis-cluster-fd959c7f4
              statefulset.kubernetes.io/pod-name=redis-cluster-0
Annotations:  <none>
Status:       Running
IP:           10.24.1.5
IPs:
  IP:           10.24.1.5
Controlled By:  StatefulSet/redis-cluster
Containers:
  redis:
    Container ID:  docker://80176bb66189813de7c7406da64e6197a1280932630b9efbb5e15558fc806eab
    Image:         redis:5.0.1-alpine
    Image ID:      docker-pullable://redis@sha256:6f1cbe37b4b486fb28e2b787de03a944a47004b7b379d0f8985760350640380b
    Ports:         6379/TCP, 16379/TCP
    Host Ports:    0/TCP, 0/TCP
    Command:
      /conf/update-node.sh
      redis-server
      /conf/redis.conf
    State:          Running
      Started:      Wed, 17 Feb 2021 21:13:17 +0000
    Ready:          True
    Restart Count:  0
    Environment:
      POD_IP:   (v1:status.podIP)
    Mounts:
      /conf from conf (rw)
      /data from data (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-7crnr (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  data:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  data-redis-cluster-0
    ReadOnly:   false
  conf:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      redis-cluster
    Optional:  false
  default-token-7crnr:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-7crnr
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>


Name:         redis-cluster-1
Namespace:    default
Priority:     0
Node:         gke-cluster-1-default-pool-0de11b8d-077v/10.154.15.199
Start Time:   Wed, 17 Feb 2021 21:13:22 +0000
Labels:       app=redis-cluster
              controller-revision-hash=redis-cluster-fd959c7f4
              statefulset.kubernetes.io/pod-name=redis-cluster-1
Annotations:  <none>
Status:       Running
IP:           10.24.2.4
IPs:
  IP:           10.24.2.4
Controlled By:  StatefulSet/redis-cluster
Containers:
  redis:
    Container ID:  docker://ed841f318a9587e7ff18eb44fa82f80f2f09fa6000ec36a12bc8b7db91948fe6
    Image:         redis:5.0.1-alpine
    Image ID:      docker-pullable://redis@sha256:6f1cbe37b4b486fb28e2b787de03a944a47004b7b379d0f8985760350640380b
    Ports:         6379/TCP, 16379/TCP
    Host Ports:    0/TCP, 0/TCP
    Command:
      /conf/update-node.sh
      redis-server
      /conf/redis.conf
    State:          Running
      Started:      Wed, 17 Feb 2021 21:13:44 +0000
    Ready:          True
    Restart Count:  0
    Environment:
      POD_IP:   (v1:status.podIP)
    Mounts:
      /conf from conf (rw)
      /data from data (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-7crnr (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  data:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  data-redis-cluster-1
    ReadOnly:   false
  conf:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      redis-cluster
    Optional:  false
  default-token-7crnr:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-7crnr
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>


Name:         redis-cluster-2
Namespace:    default
Priority:     0
Node:         gke-cluster-1-default-pool-0de11b8d-nqlk/10.154.15.198
Start Time:   Wed, 17 Feb 2021 21:13:49 +0000
Labels:       app=redis-cluster
              controller-revision-hash=redis-cluster-fd959c7f4
              statefulset.kubernetes.io/pod-name=redis-cluster-2
Annotations:  <none>
Status:       Running
IP:           10.24.1.6
IPs:
  IP:           10.24.1.6
Controlled By:  StatefulSet/redis-cluster
Containers:
  redis:
    Container ID:  docker://7d3f2601d3194e0e6f9fe80d1e697c3d847e5da6bdd95a185827f6222205bbef
    Image:         redis:5.0.1-alpine
    Image ID:      docker-pullable://redis@sha256:6f1cbe37b4b486fb28e2b787de03a944a47004b7b379d0f8985760350640380b
    Ports:         6379/TCP, 16379/TCP
    Host Ports:    0/TCP, 0/TCP
    Command:
      /conf/update-node.sh
      redis-server
      /conf/redis.conf
    State:          Running
      Started:      Wed, 17 Feb 2021 21:14:07 +0000
    Ready:          True
    Restart Count:  0
    Environment:
      POD_IP:   (v1:status.podIP)
    Mounts:
      /conf from conf (rw)
      /data from data (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-7crnr (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  data:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  data-redis-cluster-2
    ReadOnly:   false
  conf:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      redis-cluster
    Optional:  false
  default-token-7crnr:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-7crnr
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>


Name:         redis-cluster-3
Namespace:    default
Priority:     0
Node:         gke-cluster-1-default-pool-0de11b8d-077v/10.154.15.199
Start Time:   Wed, 17 Feb 2021 21:14:13 +0000
Labels:       app=redis-cluster
              controller-revision-hash=redis-cluster-fd959c7f4
              statefulset.kubernetes.io/pod-name=redis-cluster-3
Annotations:  <none>
Status:       Running
IP:           10.24.2.5
IPs:
  IP:           10.24.2.5
Controlled By:  StatefulSet/redis-cluster
Containers:
  redis:
    Container ID:  docker://c35fefcb04c9cfae51b9ed1e856903e8b87f4970dc04fb5665a4e957d22abefb
    Image:         redis:5.0.1-alpine
    Image ID:      docker-pullable://redis@sha256:6f1cbe37b4b486fb28e2b787de03a944a47004b7b379d0f8985760350640380b
    Ports:         6379/TCP, 16379/TCP
    Host Ports:    0/TCP, 0/TCP
    Command:
      /conf/update-node.sh
      redis-server
      /conf/redis.conf
    State:          Running
      Started:      Wed, 17 Feb 2021 21:14:23 +0000
    Ready:          True
    Restart Count:  0
    Environment:
      POD_IP:   (v1:status.podIP)
    Mounts:
      /conf from conf (rw)
      /data from data (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-7crnr (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  data:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  data-redis-cluster-3
    ReadOnly:   false
  conf:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      redis-cluster
    Optional:  false
  default-token-7crnr:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-7crnr
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>


Name:         redis-cluster-4
Namespace:    default
Priority:     0
Node:         gke-cluster-1-default-pool-0de11b8d-nqlk/10.154.15.198
Start Time:   Wed, 17 Feb 2021 21:14:28 +0000
Labels:       app=redis-cluster
              controller-revision-hash=redis-cluster-fd959c7f4
              statefulset.kubernetes.io/pod-name=redis-cluster-4
Annotations:  <none>
Status:       Running
IP:           10.24.1.7
IPs:
  IP:           10.24.1.7
Controlled By:  StatefulSet/redis-cluster
Containers:
  redis:
    Container ID:  docker://5a3d897d4889881bcf7c9e94801cc2d427516f8c63ac970457a152b9756777c4
    Image:         redis:5.0.1-alpine
    Image ID:      docker-pullable://redis@sha256:6f1cbe37b4b486fb28e2b787de03a944a47004b7b379d0f8985760350640380b
    Ports:         6379/TCP, 16379/TCP
    Host Ports:    0/TCP, 0/TCP
    Command:
      /conf/update-node.sh
      redis-server
      /conf/redis.conf
    State:          Running
      Started:      Wed, 17 Feb 2021 21:14:38 +0000
    Ready:          True
    Restart Count:  0
    Environment:
      POD_IP:   (v1:status.podIP)
    Mounts:
      /conf from conf (rw)
      /data from data (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-7crnr (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  data:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  data-redis-cluster-4
    ReadOnly:   false
  conf:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      redis-cluster
    Optional:  false
  default-token-7crnr:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-7crnr
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>


Name:         redis-cluster-5
Namespace:    default
Priority:     0
Node:         gke-cluster-1-default-pool-0de11b8d-077v/10.154.15.199
Start Time:   Wed, 17 Feb 2021 21:14:43 +0000
Labels:       app=redis-cluster
              controller-revision-hash=redis-cluster-fd959c7f4
              statefulset.kubernetes.io/pod-name=redis-cluster-5
Annotations:  <none>
Status:       Running
IP:           10.24.2.6
IPs:
  IP:           10.24.2.6
Controlled By:  StatefulSet/redis-cluster
Containers:
  redis:
    Container ID:  docker://b9ceb2c37bd211047a08aa92d3e86b72d5c4b748c457bf5b033e3e6e2fb4266d
    Image:         redis:5.0.1-alpine
    Image ID:      docker-pullable://redis@sha256:6f1cbe37b4b486fb28e2b787de03a944a47004b7b379d0f8985760350640380b
    Ports:         6379/TCP, 16379/TCP
    Host Ports:    0/TCP, 0/TCP
    Command:
      /conf/update-node.sh
      redis-server
      /conf/redis.conf
    State:          Running
      Started:      Wed, 17 Feb 2021 21:14:53 +0000
    Ready:          True
    Restart Count:  0
    Environment:
      POD_IP:   (v1:status.podIP)
    Mounts:
      /conf from conf (rw)
      /data from data (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-7crnr (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  data:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  data-redis-cluster-5
    ReadOnly:   false
  conf:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      redis-cluster
    Optional:  false
  default-token-7crnr:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-7crnr
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>
```

```
 k get svc
NAME            TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)                          AGE
kubernetes      ClusterIP      10.28.0.1      <none>         443/TCP                          121m
redis-cluster   LoadBalancer   10.28.10.183   35.246.52.95   6379:32160/TCP,16379:32253/TCP   79m

```


```
kubectl get pods -l app=redis-cluster -o jsonpath='{range.items[*]}{.status.podIP}:6379'
10.24.1.5:637910.24.2.4:637910.24.1.6:637910.24.2.5:637910.24.1.7:637910.24.2.6:6379%
```

```
kubectl exec -it redis-cluster-0 -- redis-cli --cluster create 10.24.1.5:6379 10.24.2.4:6379 10.24.1.6:6379 10.24.2.5:6379 10.24.1.7:6379 10.24.2.6:6379 --cluster-replicas 1
>>> Performing hash slots allocation on 6 nodes...
Master[0] -> Slots 0 - 5460
Master[1] -> Slots 5461 - 10922
Master[2] -> Slots 10923 - 16383
Adding replica 10.24.2.5:6379 to 10.24.1.5:6379
Adding replica 10.24.1.7:6379 to 10.24.2.4:6379
Adding replica 10.24.2.6:6379 to 10.24.1.6:6379
M: d96dc6b18ea905cdf9e6e9ee09f6c9146679e10e 10.24.1.5:6379
   slots:[0-5460] (5461 slots) master
M: aed45ef81cb09d15253bcd6b1f965922778b48e2 10.24.2.4:6379
   slots:[5461-10922] (5462 slots) master
M: abf4be59b996aa46f6373464b3fe0ddd86dc4059 10.24.1.6:6379
   slots:[10923-16383] (5461 slots) master
S: af98b9633c11db9a68e3220e001aa482afa1f4a7 10.24.2.5:6379
   replicates d96dc6b18ea905cdf9e6e9ee09f6c9146679e10e
S: a28a7d91c5d3ff8cfd7637ca2e1d35c5f4ba5440 10.24.1.7:6379
   replicates aed45ef81cb09d15253bcd6b1f965922778b48e2
S: 0d93c043fdb9f8b732faaee704a3ab8432029db8 10.24.2.6:6379
   replicates abf4be59b996aa46f6373464b3fe0ddd86dc4059
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join
.....
>>> Performing Cluster Check (using node 10.24.1.5:6379)
M: d96dc6b18ea905cdf9e6e9ee09f6c9146679e10e 10.24.1.5:6379
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
M: aed45ef81cb09d15253bcd6b1f965922778b48e2 10.24.2.4:6379
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
M: abf4be59b996aa46f6373464b3fe0ddd86dc4059 10.24.1.6:6379
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
S: af98b9633c11db9a68e3220e001aa482afa1f4a7 10.24.2.5:6379
   slots: (0 slots) slave
   replicates d96dc6b18ea905cdf9e6e9ee09f6c9146679e10e
S: 0d93c043fdb9f8b732faaee704a3ab8432029db8 10.24.2.6:6379
   slots: (0 slots) slave
   replicates abf4be59b996aa46f6373464b3fe0ddd86dc4059
S: a28a7d91c5d3ff8cfd7637ca2e1d35c5f4ba5440 10.24.1.7:6379
   slots: (0 slots) slave
   replicates aed45ef81cb09d15253bcd6b1f965922778b48e2
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```



