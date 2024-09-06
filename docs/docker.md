
# Docker

[Official Documentation](https://docs.docker.com/)

[OCI - Open Container Initiative](https://opencontainers.org/)

[Docker Security](https://www.aquasec.com/blog/docker-security-best-practices/)

Docker usefull commands and definitions

```bash
docker < OBJECT > < ACTION >
```

## Managing Contexts

```bash
# List Contexts
docker context ls

# Select Context
docker context use <CONTEXT NAME>
```

## Image Tagging

## Container Runtime

## Health Check

## Docker Login

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

Login to AWS ECR using command line:

```bash
aws ecr get-login-password --region <AWS REGION> | docker login --username AWS --password-stdin <AWS ACCOUNT ID>.dkr.ecr.<AWS REGION>.amazonaws.com
```

### [pass](https://www.passwordstore.org/)

### [docker-credential-helper](https://github.com/docker/docker-credential-helpers/)

### [docker-credential-osxkeychain](https://github.com/docker/docker-credential-helpers)

### [ecr-credential-helper](https://github.com/awslabs/amazon-ecr-credential-helper)

Use credHelpers for specific ECR registry based on the AWS Account ID and Region.

```json
{
   "credHelpers": {
      "public.ecr.aws": "ecr-login",
      "<AWS ACCOUNT ID>.dkr.ecr.<AWS REGION>.amazonaws.com": "ecr-login"
   }
} 
```

## Managing Networks

   TDB

## Managing Container Resources

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
--cpuset-cpus <CORES ID LIST>
```

Swap memory is used with by combining your RAM and ROM memory, the ideia is to have an
extra memory avaliably when an overhead happens, you can use  this resource protect
your application, but remember the ROM memory access is mugh slower than the RAM memory
because it is a part of your Hard Drive

## Multi Stage Build

This resource allows you to use multiple FROM command in the same Dockerfile

You can use COPY command across stages

COPY --from=< STAGE ALIAS > < OTHER STAGE PATH > < LOCAL PATH >

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
