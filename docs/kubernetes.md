# Kubernetes

Kubernetes usefull commands and definitions.

**Always be aware for command and object alias and plurals**

```bash
kubectl <ACTION> <OBJECT>
```


## Managing Contexts

You can manage the contexts using the following command's or by using and extension/plugin [kubectx](https://github.com/ahmetb/kubectx?tab=readme-ov-file#kubectl-plugins-macos-and-linux)

```bash
# List Contexts
kubectl config get-contexts

# Select Context
kubectl config use-context <CONTEXT NAME>

# Rename Context Alias
kubectl config rename-context <CURRENT NAME> <NEW NAME>
```


## Port Foward

```bash
kubectl port-forward service/<SERVICE NAME> <LOCAL PORT>:<SERVICE PORT> -n <NAMESPACE>
```
SERVICE PORT will be 80 if using svc


## Internal DNS

This is how you can access your services inside your cluster whitout exposing the traffic to the internet

A fully qualified domain name (FQDN) is the complete domain name for a specific computer, or host, on the internet. The FQDN consists of two parts: the hostname and the domain name.

```bash
Headless: <SERVICE>.<NAMESPACE>.svc.cluster.local
```
