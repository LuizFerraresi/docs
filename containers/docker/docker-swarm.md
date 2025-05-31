
# Docker Swarm

[Website]()

## Design

exposes 2377

Nodes must be able to reach each other on:

TCP/2377 (Swarm management)

TCP/7946 and UDP/7946 (cluster communication)

UDP/4789 (overlay network traffic)

## Commands

### Create cluser manager node

```bash
# create swarm cluster manager
docker swarm init --advertise-addr [MANAGER NODE IP]

# create your resources using a docker-compose.yaml file
docker stack deploy -c/--compose-file path/to/docker-compose.yaml [STACK NAME]

# generate cluster token to add more manager or worker nodesn `-q` - says to return a string value
docker swarm join-token -q [worker|manager]

# join a node to a cluster
docker swarm join --listen-addr --token [TOKEN VALUE]
```
