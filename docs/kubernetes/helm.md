# [Helm](https://helm.sh/docs/intro/using_helm/)

Helm is a Kubernetes package manager

A Chart is a Helm package. It contains all of the resource definitions necessary to run
an application, tool, or service inside of a Kubernetes cluster.

> [!IMPORTANT]
> from major release ``v2`` to ``v3`` helm had a break change in it's design, it left
> ``tigger`` to call you cluster API directly.

When using charts you will combine your chart templte with all kubernetes defirion and
combine it with your application custom values from your app ``values.yaml`` file

> [!TIP]
> It's a good practive to use one file per resource, so if you use multiples definitions
> for the same service you will keep the togheter, spacing them with ``---`` with
> indicates that you are using multiple yamls in the same file.


[ArtifactHUB](https://artifacthub.io/)

It uses ``go`` project

## Install

```bash
brew install helm
```

## Commands

Arguments:

- ``-f/--values``  path/to/values.yaml

### Adding a repository

```bash
helm repo add < REPOSITORY NAME > < REPOSITORY SOURCE >
helm repo update  # update local repository list
```

helm show values > values.yaml

--set apth.key=value can overwrite values.yaml vaçues when creating the application definition file on fly.

### Deployng a Chart

## Folder Structure

```bash
/
- appset/
    - < APPLICATION NAME >/
        - value.yaml
- templates/
    - < TEMPLATE NAME >/
        - NOTES.txt  # Gives user prompt responses
```

{{ .Releaste }}  # release metadata

## Control Flow (If/Else Statements)

A good practice is to always add a defalt block config with else,
and always add a defalt value to the config and always check the keys when trying to reach some value

## Charts Dependency

### TDB
