
# Docker Swarm

[Website]()

## Design

## Commands

### Create cluser manager node

```bash
docker swarm init --advertise-addr [MANAGER NODE IP]
```

### Create your resources using a docker-compose.yaml file

```bash
docker stack deploy -c path/to/docker-compose.yaml [STACK NAME]
```

### Generate cluster token to add more manager or worker nodes:

```bash
docker swarm join-token -q [worker|manager]
```

`-q` - says to return a string value

### Join a node to a cluster

```bash
docker swarm join --token [TOKEN VALUE]
```
