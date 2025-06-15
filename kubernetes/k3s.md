
# K3S

[Website](https://docs.k3s.io/) | [Github](https://github.com/k3s-io/k3s)

## Installation

### Simple Installation

```bash
curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION="v1.31.2+k3s1" sh -s - \
  --node-name [NODE NAME]
```

It will be created in `default` context.

### Custom Config

It's possible to configure cluster master deployment with arguments or environment variables on shell command or use  `--config=/path/to/file/cluster-config.yaml` parameter when using shell command.

## Deployment Strategies



## Disabling Default CNI

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

### Config File

```yaml
# /etc/rancher/k3s/config.yaml
flannel-backend: "none"
disable-kube-proxy: true
disable-network-policy: true
cluster-init: true
disable:
  - servicelb
  - traefik
```

Create using the config file

```bash
curl -sfL https://get.k3s.io | sh -s - --config=$HOME/config.yaml
```

## Raw Cluster

### Command Line

```bash
curl -sfL https://get.k3s.io | sh -s - \
  --node-name [NODE NAME]
  --flannel-backend none \    # disable fannel backend
  --disable-kube-proxy \      # disable bukeproxy daemont set
  --disable-network-policy \  # disable kube-routers network fpolicy
  --disable traefik \         # disable traefik ingress
  --disable servicelb \       # disable servicelb as load balancer provider
  --disable coredns \         # disable coredns     
  --disable metrics-server \  # disable metrics server
  --cluster-init
```

## Container Runtime

```bash
curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION="v1.31.2+k3s1" sh -s - \
  --docker # enabled docker runtime, defaults contdainerd
```

## Token Managment

Create cluster bootstrapping token:

```bash
K3S_TOKEN=$(k3s token create)
```

## Cluster IP

Get cluster IP

```bash
hostname -I
```

default port: 6443

## Add Agent Nodes

```bash
K3S_TOKEN=<TOKEN>
API_SERVER_IP=<IP>
API_SERVER_PORT=<PORT>
curl -sfL https://get.k3s.io | sh -s - agent \
  --token "${K3S_TOKEN}" \
  --server "https://${API_SERVER_IP}:${API_SERVER_PORT}"
```

## Add Control Plane Nodes

```bash
K3S_TOKEN=<TOKEN>
API_SERVER_IP=<IP>
API_SERVER_PORT=<PORT>
curl -sfL https://get.k3s.io | sh -s - server \
  --token ${K3S_TOKEN} \
  --server "https://${API_SERVER_IP}:${API_SERVER_PORT}" \
  --flannel-backend=none \
  --disable-kube-proxy \
  --disable-network-policy \
  --disable servicelb \
  --disable traefik
```

## Remove Node

```bash
kubectl drain [NODE NAME] --ignore-daemonsets --delete-local-data
```

```bash
kubectl delete node [NODE NAME]
```

## Restart Cluster

Master Node

```bash
sudo systemctl restart k3s
```

Agent Nodes

```bash
sudo systemctl restart k3s-agent
```

## Uninstall Cluster

Master Node

```bash
sudo /usr/local/bin/k3s-uninstall.sh
```

Agent Nodes

```bash
sudo /usr/local/bin/k3s-agent-uninstall.sh
```

Kill All

```bash
sudo /usr/local/bin/k3s-killall.sh
```

Remose residual data

```bash
sudo rm -rf /etc/rancher/k3s /var/lib/rancher/k3s
```

Clean previous installations

```bash
rm -rf /var/lib/rancher /etc/rancher; \
ip addr flush dev lo; \
ip addr add 127.0.0.1/8 dev lo;
```

## Accessing Cluster

```bash
export KUBECONFIG=~/.kube/config
```

### Credentials from K3S

```bash
sudo k3s kubectl config view --raw > "$KUBECONFIG"
```

### Credentials from Rancher

```bash
/etc/rancher/k3s/k3s.yaml > "$KUBECONFIG"
```

### Configure `KUBECONFIG` Permission

```bash
chmod 600 "$KUBECONFIG"
```

> [!CAUTION]
> The `etc/rancher/k3s/k3s.yaml` path should not hve it's permission changed, it should not be accessed by external sources,
> to solve this we simply copy the config to the correct path in `/.kube/config`.

Now `kubectl` commands should be avaliable

## Troubleshooting

´´´bash
sudo systemctl status k3s.service
```

```bash
sudo journalctl -xeu k3s.service
```
