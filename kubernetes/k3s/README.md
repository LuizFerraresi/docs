
# K3S - Lightweight Kubernetes by Rancher

[Website](https://docs.k3s.io/) | [Github](https://github.com/k3s-io/k3s)

default api port: `6443`

default coredns ip: 10.43.0.10

## Installation

### Simple Installation

```bash
curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION="v1.31.2+k3s1" sh -s - \
  --docker
```

`--dcoker` - enabled docker runtime, defaults `contdainerd`.

### Cluster Installation

`--cluster-init` - start in cluster mode

> [!WARNING]
> node name must be unique across the cluster, if your instances has the same name,use `--node-name` to provide
> an unique identification for each node

HA with embeded etc and kube-vip

```bash
curl -sfL https://get.k3s.io | K3S_TOKEN=[SECRET] sh -s - server \
    --cluster-init \
    --tls-san=</[FIXED IP] # Optional, needed if using a fixed registration address
```

### Raw Cluster Deploy

#### Command Line

```bash
curl -sfL https://get.k3s.io | sh -s - \
  --node-name [NODE NAME]
  --flannel-backend none \    # disable fannel backend
  --disable-kube-proxy \      # disable bukeproxy daemont set
  --disable-network-policy \  # disable kube-routers network policy
  --disable traefik \         # disable traefik ingress
  --disable servicelb \       # disable servicelb as load balancer provider
  --disable coredns \         # disable coredns     
  --disable metrics-server \  # disable metrics server
  --cluster-init
```

### Config File

It's possible to configure cluster master deployment with arguments or environment variables on shell command 
or use  `--config=/path/to/file/cluster-config.yaml` parameter.

```yaml
# /etc/rancher/k3s/config.yaml
flannel-backend: "none"
disable-kube-proxy: true
disable-network-policy: true
disable:
  - traefik
  - servicelb
  - coredns
  - metrics-server
cluster-init: true
```

Create using the config file

```bash
curl -sfL https://get.k3s.io | sh -s - --config=$HOME/config.yaml
```

## Token Managment

Create cluster bootstrapping token:

```bash
K3S_TOKEN=$(k3s token create)
```

## Add Agent Nodes

```bash
K3S_TOKEN=[TOKEN]
API_SERVER_IP=[IP]

curl -sfL https://get.k3s.io | sh -s - agent \
  --token "${K3S_TOKEN}" \
  --server "https://${API_SERVER_IP}:6443"
```

## Add Control Plane Nodes

```bash
K3S_TOKEN=[TOKEN]
API_SERVER_IP=[IP]

curl -sfL https://get.k3s.io | sh -s - server \
  --token ${K3S_TOKEN} \
  --server "https://${API_SERVER_IP}:6443" \
  --flannel-backend=none \
  --disable-kube-proxy \
  --disable-network-policy \
  --disable servicelb \
  --disable traefik
```

## Remove Node

```bash
# stop pod scheduling on the node
kubectl cordon [NODE NAME]

# remove pods from the node
kubectl drain [NODE NAME] --ignore-daemonsets --delete-local-data

# remove node
kubectl delete node [NODE NAME]
```

## Restart Cluster

```bash
# master node
sudo systemctl restart k3s

# agent nodes
sudo systemctl restart k3s-agent
```

## Uninstall Cluster

```bash
# uninstall master node
sudo /usr/local/bin/k3s-uninstall.sh

# uninstall agent node
sudo /usr/local/bin/k3s-agent-uninstall.sh

# kill all
sudo /usr/local/bin/k3s-killall.sh

# remove residual data
sudo rm -rf /etc/rancher/k3s /var/lib/rancher/k3s

# cleanup previous installations
rm -rf /var/lib/rancher /etc/rancher; \
ip addr flush dev lo; \
ip addr add 127.0.0.1/8 dev lo;
```

## Cluster Credentials

```bash
export KUBECONFIG=~/.kube/config
```

```bash
# credentials from k3s
k3s kubectl config view --raw > "$KUBECONFIG"

# credentials from rancher (avoid)
sudo cat /etc/rancher/k3s/k3s.yaml > "$KUBECONFIG"
```

It will be created in `default` context.

Now `kubectl` commands should be avaliable

### Configure `KUBECONFIG` Permission

```bash
chmod 600 "$KUBECONFIG"
```

> [!CAUTION]
> The `etc/rancher/k3s/k3s.yaml` path should not have it's permission changed, it should not be accessed by external sources,
> to solve this we simply copy the config to the correct path in `/.kube/config`.

## Troubleshooting

```bash
# service status
sudo systemctl status k3s.service

# restar service
sudo systemctl restart k3s
```

```bash
# service logs
sudo journalctl -u k3s -n 30 --no-pager
```

```bash
# check if API is listening on port 6443
sudo netstat -tulnp | grep 6443
```

## K3S + Clilium

The defaults CNI consists of `CoreDNS`, `Flannel`, `kube-router`, `ServiceLB` (Klipper LoadBalancer) and `Traefik`.

[K3S + Cilium](https://blog.stonegarden.dev/articles/2024/02/bootstrapping-k3s-with-cilium/#tips-tricks-and-troubleshooting)

Whithout cluster CNI (Container Network Interface):

### Command Line

```bash
curl -sfL https://get.k3s.io | sh -s - \
  --node-name [NODE NAME]
  --flannel-backend none \
  --disable-kube-proxy \
  --disable-network-policy \
  --disable traefik \
  --disable servicelb \
  --disable coredns \
  --cluster-init
```

```bash

```
