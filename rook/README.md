# Kubernetes Rook Cluster

### Enable Rook  on all Nodes

```
  apt-get  update                         # BOTH MASTER AND NODES
  apt-get -y install ceph-common             # BOTH MASTER AND NODES
```

### Start/Initialize cluster

```
...
```

### Fetch this repo

```
  curl https://codeload.github.com/mikeatm/kubernetes-redis-cluster/tar.gz/master | tar -xz
```

### Setup storage

```
  kubectl create -f rook-operator.yaml      # Edit with:  FLEXVOLUME_DIR_PATH = "what you want"
```

### Ensure rook-operator  and rook-agent pods  are running

```
  kubectl -n rook-system get pod
```

### Setup RBAC

```
  kubectl create -f other-rbac.yaml
```

### Setup the Rook Cluster

```
  kubectl create -f rook-cluster.yaml
```

### Ensure all pods are running:

```
  kubectl -n rook get pod
```

### Create Storage Class:

```
  kubectl create -f rook-storageclass.yaml
```

### Clean Up

```
  kubectl delete -n rook pool replicapool
  kubectl delete storageclass rook-block
```

