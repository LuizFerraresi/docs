
# kind (Kubernetes in Docker)

[Website](https://kind.sigs.k8s.io/)

This is a fast way to deploy and test Kubernetes, as with a simple docker run and docker kill
you can create and destroy a cluster, and you can deploy a multi node cluster

When creating a cluster it will also create a ``kind-`` prefixed context followed by your cluster name

The cluster config on ``~/kubectl/config`` will point to the master docker and it will resolve to wich
container it should be proxy

## Installation

## Create a cluster

```bash
kind create cluster \
  --name [CLUSTER NAME]\
  --image kindtest/node:[KUBERNETES VERSION] \
  --config kind.yaml
```

Kind Cluster Definition file

```yaml
# kind.yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
name: tst-cluster
nodes:
  - role: control-plane
  - role: worker
  - role: worker
  - role: worker
networking:
  apiServerAddress: "127.0.0.1"
  apiServerPort: 6443
  disableDefaultCNI: false  # if true, the default CNI will not be installed
```

More node options

```yaml
nodes:
  - role: worker
    labels:  # add labels to the node
```
