
![Docker Image](src=)

# Docker

[Website](https://www.docker.com/) |
[Github](https://github.com/docker)

[OCI - Open Container Initiative](https://opencontainers.org/)

[Docker Compose](./docs/docker-compose.md)

[Docker Swarm](./docs/docker-swarm.md)

## Virtualization

![Virtualization Image](src=)

### Baremetal (Virtual-Machine)

Not immutable, hard to manage external libs

### Docker Engine

Containers isolates the application OS frm the host one.

O Container Engine, é muito mais leve e mis versátil. Não precisamos mais virtualizar tudo independentemente. sem usar o meus OS e sem carregar o mesmo OS multiplas vezes.

The containers are threated as proccesses by the host

- Resources Optimization
- Application Packing
- Imutabiliy
- Deploy made easy

## Isolation

![Isolation Image](src=)

### namespaces

`PID` - Process ID - Container process isolation

`NET` - Network interfaces isolation

`IPC` - Isolation between process and shared memory

`MNT` - Volumes isolation

`UTS` - Kernel isolation, act as a container is another host

### cgroups

It enables the possibity of setting/restricting the recource accessed by a container, it can be done automatically ou manually

The reosurces are managed by....

## Engine

![Docker Objects and Commands Images](src=)

## Install

After installing, run the following command to run it without ``sudo``, by adding the user to the docker group

```bash
sudo usermod -aG docker $USER
```

```bash
# make it start with OS
sudo systemctl enable docker

# check status
sudo systemctl status docker
```

## Commands

```bash
# show reclaimable memory (Disk Space)
docker system df

# clean reclaimable memory (Disk Space)
docker system prune -a

# enter in container prompt
docker exec -it [CONTAINER ID] [sh | bash]

# list containers in execution
docker ps

# check data from an image
docker inspect [IMAGE ID]

# check the hisotry of an image
docker history [IMAGE ID]
```

## Contexts

```bash
# list contexts
docker context ls

# select context
docker context use [CONTEXT NAME]
```

## Image Registry

A Registry is a collection of images wich can be downloaded.

### Public

`DockerHub` | `Github Packages`

### Private

Check if the image registry have a vulnerabilty scan enabled or at least integration compatibility with some security software.

> [!TIP]
> the `DIGEST sha256` is an image validation after the pull to check if the image matches the disired the required one and if there is no malware on the binary.

```bash
docker pull [IMANGE NAME]:[IMAGE TAG | latest]
```

When docker pull is executed we actually pull the image layers, not the image itself, docker first searches locally for the layers before pulling it again, avoid content deduplication, it only creates or pull and alyer again (loses cache) if a previously layer is changed.

```bash
docker push [IMAGE NAME]:[IMAGE TAG]
```

## Registry Login

Your docker repository auth config is located in ``~/.docker/config.json``

```bash
# Login to a self-hosted-registry
docker login localhost:8080
```

### Docker Credential Store

Using external credentials store is safer than using docker configuration file, to
enable it by changing ``~/.docker/config.json``

```json
{
  "credsStore": "ecr-login" | "osxkeychain" | "pass"
}
```

> [!IMPORTANT]
> If no credential store is set, the secrets are stored as base64 encoded in config file.

> [!TIP]
> Se tiver algum problema com o login, apenas deixe o valoes de `"credHelpers":""` como uma string varia e rode o commando de login novamnte

### [ecr-credential-helper](https://github.com/awslabs/amazon-ecr-credential-helper)

Use credHelpers for specific ECR registry based on the AWS Account ID and Region.

```json
{
   "credHelpers": {
      "public.ecr.aws": "ecr-login",
      "[AWS ACCOUNT ID].dkr.ecr.[AWS REGION].amazonaws.com": "ecr-login"
   }
} 
```

Login to AWS ECR using command line:

```bash
aws ecr get-login-password --region [AWS REGION] | docker login --username AWS --password-stdin [AWS ACCOUNT ID].dkr.ecr.[AWS REGION].amazonaws.com
```

## Dockerfile

Contains the instructions to create the Image Layers

Alaways start with command with less probability of changing, as the following commands depends on the privious one, if a middle layer is changed, all the following layers will be reprocessed.

Everything starts with a base imagem from the registry.

`FROM` - Pull the base image from registry

```dockerfile
FROM scratch
# or
FROM [IMAGE NAME]:[IMAGE TAG]
# or
FROM [IMAGE NAME]:[IMAGE TAG] AS [STAGE ALIAS]
# or
FROM [STAGE ALIAS] AS [CURRENT STAGE ALIAS]
```

`RUN` - Execute commands

`COPY` - Copy host files to the image

`ADD` - Add files to the image or download it from a URL, if a .tar file is copied it will automatically unzip it.

`CMD` - Final command to run the application, this type can be overwrite outiside container with `-c` flag on ``docker run`` command

```dockerfile
CMD ["executable", "params"]
```

`ENTRYPOINT` - Final command to run the application, canot be overwrite

```dockerfile
ENDRYPOINT ["executable", "params"]
# or
ENTRYPINT ["executable"]
CMD ["params"]
```

`WORKDIR` - Set the container root directory to copy files and run commands, this will be the folder to be opened when interactive mode is executed

`EXPOSE` - Shows wich port on the container we wish to expose, the protocol can also be specified

`ARG` - Values avaliable only during container build proccess

`ENV` - Values used during container runtime

`HEALTHCHECK` - check container health by running a command inside the container or disable health check from base image

```dockerfile
HEALTHCHECK NONE
# or
HEALTHCHECK --interval=5m --timeout=3s \
  CMD curl -f http://[HOST]/[PORT] || exit 1
```

`USER` - 

`LABEL` - You can use labels to organize your images.A label is a key-value pair, stored as a string. You can specify multiple labels for an object, but each key must be unique within an object. Keys are alphanumeric strings which may contain periods (.), underscores (_), slashes (/), and hyphens (-)

[CMD and ENTRYPOINT Interaction](https://docs.docker.com/reference/dockerfile/#understand-how-cmd-and-entrypoint-interact)

[OCI Annotations](https://github.com/opencontainers/image-spec/blob/main/annotations.md)

### Multi Stage Build

The idea behind this feature is to build multiple images inside the same dokerfile.

You can create lighter images to use when running ou storing it, you can leave all install and deploy layers behind, building a lighter image in the end and reusing elements from other builds (layers executed inside one docker image that not the last one, or the final build).

Multi Stag builds consists in Dockerfiles with more than one FROM statement where they can be named using AS ou they can be referenced by the order of image imports, starting from 0

![Multi Stage Build Image](src=)

As it's possible to declare multiple `FROM` statements, we can have multiple base images and to allow an external reference for thoose bases or even hierentance, it's possible to set alias with `AS` statement.

https://www.youtube.com/watch?v=hmpUegtHG9s

This resource allows you to use multiple FROM command in the same Dockerfile

You can use COPY command across stages

```dockerfile
# Dockerfile
FROM [IMAGE NAME]:[IMAGE TAG] AS [SOURCE STAGE ALIAS]
...

FROM [IMAGE NAME:IMAGE TAG | SOURCE STAGE ALIAS] AS final

COPY --from=[SOURCE STAGE ALIAS] [SOURCE STAGE PATH] [CURRENT STAGE PATH]
```

> [!TIP] Managging Multiple Base Images
> `Dockerfile.[BASE IMAGE].yaml`

## Image

Is a combination of stacked layers, which one with it's own identifier, that together defines an Image.

Is a static bynary file used to run the container

The base image /, or the `FROM` statement, is unchangeble beause it contains the Container Kernel, the container instance structure, access controls and the namespaces.

The image layers are Read-Only

### Tagging

If the image is build whitout `-t/--name` argument it will be set as `latest` by default

```bash
docker build -t/--name
```

```bash
docker tag [IMAGE NAME]:[IMAGE TAG] [NEW IMAGE NAME]:[NEW IMAGE TAG]
```

> [!WARNING]
> Private repositories may require to specify the repository address/host as a prefix to the image name

## Container

The Containers are Image layers with a Read-Write layer added on top of it, being lightweight to keep containers fast during it instance and runtime.

As Images are RO it's possible to use the same image in multiples containers, having only a different RW layer on each Container.

The Container will only keep running while a proccess is active or in execution, if we do not have a long-lived proccess application, the container will be exit after ouy proccess is complete.

> [!WARNING]
> Zumbi Containers - The container is started but there is no active proccess, so it exit right away.

## Build

> [!TIP]
> You can use .dockerignore file to exclude files and directories from the build context.

```bash
docker buildx build \
  --build-arg FOO=bar \           # allows you to pass the build-time variables that are accessed like regular environment variables
  --file ./Dockerfile \           # path to Dockerfile
  --label foo=bar \               # Set metadata for an image
  --platform local \              # os/arch | os/arch/vX | linux/amd64 | linux/arm/vX | local
  --progress plain \              # auto | plain | tty | rawjson
  --ssh default \                 # exposes SSH agent socket or keys to the build
  --target [STAGE ALIAS NAME]     # specify an intermediate build stage by name as a final stage for the resulting image
  --tag [IMAGE NAME]:[IMAGE TAG]
```

You can use ``docker buildx build`` or ``BUILDKIT=1 docker build`` it will have the same result.

### SSH Authentication

when you use ``--ssh`` can be used with the RUN ``--mount=type=ssh`` mount and pass ``SSH_AUTH_SOCK`` environment variable.

> [!IMPORTANT]
> This is pretty handy when using private repositories as package namagers

```bash

```

```yaml
# docker-compose.yaml
services:
  example-service:
    build:
      ssh: default
    environment:
      ${SSH_AUTH_SOCK}: ${SSH_AUTH_SOCK}
```

```dockerfile
FROM alpine:latest

ENV SSH_AUTH_SOCK=${SSH_AUTH_SOCK}

RUN apk update && \
    apk --upgrade

RUN --mount=type=ssh

RUN [DOWNLOAD PACKAGE]
```

## Run

```bash
docker run \
  --env NAME=VALUE \                                   # Set environment variables
  --env-file path/to/file.ext \                        # Read in a file of environment variables
  --name [PROJECT NAME] \                              # Assign a name to the container
  [IMAGE NAME]:[IMAGE TAG | latest] \
  -h, --hostname [HOSTNAME | 0.0.0.0.0 | localhost] \  # Container host name
  -p, --publish \                                      # Publish a container's port(s) to the host
  -d \                                                 # Run container in background and print container ID
  -i[COMMAND]                                          # Keep STDIN open even if not attached
```

### CLI

```bash
# create and run containers
docker compose -f/--file [PATH/docker-compose.yaml] up --build

# start specific container 
docker compose

docker compose kill

docker compose down
```

### Multiple docker-compose.yaml files

`docker-compose.[spacific usage].yaml`

```bash
docker compose \
  -f docker-compose.yaml \
  -f docker-compose.dependencies.yaml \
  up --build
```

## Network

Grant container comunication in the same network.

The created networks can make DNS resolution from IP to hostname's, the common/default networks does not have this feature.

The configurations are provided to an intra net system, the calls does not goes outside the network and them comeback to the other service, they are all made inside the defined network.

O Docker cria regras automaticamente na maquina host para que containeres em diferentes redes nao se comuniquem diretamente.

> [!TIP]
> It's possible to install plugin's from third party.

### bridge

Used to comunicate containers in the same docker daemon host.

Is a sharing network.

The created containers are connected to it by default.

The created bridge networks allows the communication by hostname's and are better than the default one.

```bash
docker network create
```

É um dispositivo de camada de enlace que encaminha o trafego entre segmentos de rede.

Uma bridge pode ser um dispositivo de hardware ou um dispositivo de software executa no kernel de uma maquina host

### host

The host network system, if attached to a container, all the restrictions betwween the host and the container network are removes, as the same interface is being used.

### null

Named as none by default.

When this drive is attached to a container, we are removing all the network interfaces from it, making it off grid, removing all connection/communication capabilities. Should be used when using custom network drivers.

Esta disponivel para sistemas de enxame

### overlay

Sao redes de sobreposiçao e conectam varions daemons do docker e permite q sistemas se enxame comuniquem-se entre si

Essa estrategia eliminaa necessidade de roteamento no nivel de OS

### ipvlan

### macvlan

```yaml
# docker-compose.yaml
networks:
  bridge-network:
    driver: bridge
  host-network:
  overlay-network:

services:
  application:
    networks:
      - [NETWORK NAME]
```

## Volumes

> [!TIP]
> / path from the root directory
> ./ or ${PWD}/ to use local files reference

### volume

Docker managed file system area from host, being safer during executing time.

As the volumes are managed by Docker it's not required to set the host folder path on `-v/--volume` option, just pass the volume name and the container path.

The volumes are stored in `/home/$USER/var/lib/docker/volumes`

It can be created using `--mount`

```bash
--mount source=volume_name, target=container_path
```

THE VOLUME TYPE IS NOT REQUIRED TO BE PASSED AS ARGUMENT, AI IT is already the default.

### bind

It's capable of persisting data throught the link created with the host folder structure, it's only required to pass the host and container path to be synced.

```bash
--mount type=bind, source=host_path, target=container_path
```

> [!WARNING]
> The host directory folder's should be created manually, and the container directory is automaticaly created.

### tmpfs

> [!WARNING]
> Works on Linus Host only

Create a temporary directory inside the RAM memory/volitile memmory and not being mounted above R/W layer from the container.

This type of volume has it's own argument `--tmpfs`

```bash
--tmpfs=container_path
```

```bash
--mount type=tmpfs,destination=container_path
```

As pastas com o fundo pintado, indicam que sao pastasr armazenadas em memória e nao em disco

```yaml
# docker-compose.yaml
volumes:
  app-volume:
  app-bind:
  app-tmpfs:

services:
  applications:
    volumes:
      - [VOLUME NAME]
```

## Secrets

They can be used both in build and run stages

### file

```bash

```

### environemnt

```bash

```

```yaml
# docker-compose.yaml
secrets:
  env-secret:
    environment: ENV_VAR_NAME
  file-secret:
    file: secret.txt

services:
  application:
    build:
      secrets:  # secret avaliable only during building proccess
        - [SECRET NAME]
    secrets:
      - [SECRET NAME]
```

To use the secrets in a dockerfile command, you shiuld specifi it

```dockerfile

```

## Container Resources

> [!IMPORTANT]
> All hosts resources are avaliable by default

We can limit memory, swap and cpu.

```bash
docker run
  --cpus < int > \
  --memory < int > \  # b | mb | gb 
  --memory-swap < int > # memory + swap  # b | mb | gb 
```

We can set cpu by giving the number of cores, it's possible to use float numbers
as you can use a percentage of a core.

Uses de ``completely fair`` scheduler with linux kernel, you can set with core tho use
to force the core if needed. If a float number is passed, you should always round up.
The processing will be distributed within the cores selected.

```bash
--cpuset-cpus [CORES ID LIST]
```

Swap memory is used with by combining your RAM and ROM memory, the ideia is to have an
extra memory avaliably when an overhead happens, you can use  this resource protect
your application, but remember the ROM memory access is mugh slower than the RAM memory
because it is a part of your Hard Drive

```yaml
# docker-compose.yaml
servcies:
  application:
    deploy:
      resources:

     
```

## Container Security

[Docker Security Best Practices](https://www.aquasec.com/blog/docker-security-best-practices/)

### Docker Scout

It's a security tools to seek vulnerabilities in our image, the scan is only done in packages, os, config files and other elements of our Image

```bash
# run complete vulnerability analysis
docker scout nves

# compare two images vulnerabilities
docker scout compare --to [IMAGE NAME]:[IMAGE TAG] [IMAGE NAME]:[IMAGE TAG]
```
