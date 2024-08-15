# Docker

[OCI - Open Container Initiative](https://opencontainers.org/)

Docker usefull commands and definitions

```bash
docker <OBJECT> <ACTION>
```

## Managing Contexts

```bash
# List Contexts
docker context ls

# Select Context
docker context use < CONTEXT NAME >
```

## Usefull Commands

### Show reclaimable memory (Disk Space)

```bash
docker system df
```

### Clean reclaimable memory (Disk Space)

```bash
docker system prune -a
```

### Enter in container prompt

```bash
docker exec -it < CONTAINER ID > sh
```
