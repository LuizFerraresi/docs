# [Helm](https://helm.sh/docs/intro/using_helm/)

Helm is a Kubernetes package manager.

A Chart is a Helm package. It contains all of the resource definitions necessary to run
an application inside of a Kubernetes cluster.

It uses ``Go template language`` and some other plugins to generate the definition files.

> [!IMPORTANT]
> from major release ``v2`` to ``v3`` helm had a break change in it's design, it left
> ``tigger`` to call you cluster API directly.

When using charts you will combine your chart templte with all kubernetes definition and
combine it with your application custom values from your app ``values.yaml`` file.

> [!TIP]
> It's a good practive to use one file per resource, so if you use multiples definitions
> for the same service you will keep the togheter, spacing them with ``---`` with
> indicates that you are using multiple yamls in the same file.

It's possible tyo use `.helmignore` to set the files to be ignored by helm engine.

## Install

```bash
brew install helm
```

## Repositories

[ArtifactHUB](https://artifacthub.io/)

### Add a Repository

```bash
helm repo add <REPOSITORY NAME> <REPOSITORY SOURCE>
```

### Update Repositories Stored Locally

```bash
helm repo update
```

## Charts

### Install from Source

```bash
helm install <HELM NAME> <REPO NAME>/<HELM NAME> \
    --namespace <NAMESPACE>
```

### Install from Local

```bash
helm install <HELM NAME> <REPO NAME>/<HELM NAME> \
    --namespace <NAMESPACE> \
    --values values.yaml
```

### Install Definition Files

full definition installation -> add crd and resources definitions to local folder sistem,

## Folder Structure

These are the minimum requirements for deploying a Chart

```bash
|- project/
|    |- Chart.yaml
|    |- values.yaml
|    |- templates/
|        |- NOTES.txt
|        |- template files
```

## Required Files

```yaml
# Chart.yaml
apiVersion: v2     # specify Helm API version, v1 for Helm 2 and v2 for Helm 3
appVersion: 1.0.0  # specifying the version of the application
description: A Helm chart for Kubernetes
name: common
type: application | library
version: 1.0.0
```

The library type chart provides utilities or functions for the chart builder. A library chart differs from an application chart because it is not installable and usually doesn't contain any resource objects.

The values file can be used on the template files for kubernetes resources definitions as in chart dependencies

```yaml
# values.yaml
app:
  name:

```

The `NOTES.txt` file values, will be shown on prompt when running `helm install`, it's also
possible to use `.Values` references.

```txt
# NOTES.txt
Application {{ .Release.Name }} deployed on {{ Release.Namespace }}
```

All the files inside the `templates` folder will pass throught helm engine, the default
values will be loaded from `values.yaml` file and/or `--set path.key=value` can
overwrite values on fly.

## Values References on Definition Files

{{ .Values }} are suplied by `values.yaml` or by `--set` argument

We also have some predefined values thata cannot be overwritten:

`Release.Name`: The name of the release (not the chart)

`Release.Namespace`: The namespace the chart was released to.

`Release.Service`: The service that conducted the release.

`Chart`: The contents of the Chart.yaml.

## Control Flow (If/Else Statements)

> [!TIP]
> A good practice is to always add a defalt block config with else, and always add a
> defalt value to the config and always check the keys when trying to reach some value
