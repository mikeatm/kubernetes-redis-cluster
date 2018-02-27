# Kubernetes Redis Cluster

### Create Rook Hyperconverged Storage

[see inside rook folder for Hyperconverged storage setup](rook/README.md)


### Create Redis Cluster Configuration

```
kubectl create configmap redis-conf --from-file=redis.conf
```

### Create Redis Services

```
kubectl create -f statefulset-services/redis-headless.yaml
```

### Create Redis Statefulset

```
kubectl create -f statefulset/redis-statefulset.yaml
```

### Connect Nodes

```
kubectl run -i --tty ubuntu --image=ubuntu  /bin/bash
```

```
apt-get update
apt-get install -y vim wget python2.7 python-pip redis-tools dnsutils
```

*Note:* `redis-trib` doesn't support hostnames (see [this issue](https://github.com/antirez/redis/issues/2565)), so we use `dig` to resolve our cluster IPs.

```
pip install redis-trib
```

```
redis-trib.py create \
  `dig +short redis-app-0.redis-service.default.svc.cluster.local`:6379 \
  `dig +short redis-app-1.redis-service.default.svc.cluster.local`:6379 \
  `dig +short redis-app-2.redis-service.default.svc.cluster.local`:6379

redis-trib.py replicate \
  --master-addr `dig +short redis-app-0.redis-service.default.svc.cluster.local`:6379 \
  --slave-addr `dig +short redis-app-3.redis-service.default.svc.cluster.local`:6379
redis-trib.py replicate \
  --master-addr `dig +short redis-app-1.redis-service.default.svc.cluster.local`:6379 \
  --slave-addr `dig +short redis-app-4.redis-service.default.svc.cluster.local`:6379
redis-trib.py replicate \
  --master-addr `dig +short redis-app-2.redis-service.default.svc.cluster.local`:6379 \
  --slave-addr `dig +short redis-app-5.redis-service.default.svc.cluster.local`:6379
```

### Accessing redis cli

Connect to ubuntu  pod

```
kubectl exec -it ubuntu -- /bin/bash
```
Access cli

```
  redis-cli -c -p 6379 -h `dig +short redis-app-2.redis-service.default.svc.cluster.local`
```
To check cluster nodes

```
  redis-cli -p 6379 -h `dig +short redis-app-2.redis-service.default.svc.cluster.local` cluster nodes
```

### Clean up,

```
  kubectl delete -f persistentvolumeclaims
  kubectl delete -f statefulset-services/redis-headless.yaml
  kubectl delete -f statefulset/redis-statefulset.yaml
```

### Contribs

```
Originally contributed by Kelsey Hightower (https://github.com/kelseyhightower/kubernetes-redis-cluster)
```
