# Kubernetes

Kubernetes usefull commands and definitions.

[Config Best Practices](https://kubernetes.io/docs/concepts/configuration/overview/)

> [!WARNING]
> **Always be aware for command and object alias and plurals**

```bash
kubectl <ACTION> <RESOURCE>
```

## Kubectl Extensions with [krew](https://krew.sigs.k8s.io/)

Krew is the plugin manager for kubectl command-line tool.

```bash
kubectl krew install <EXTENSION-NAME>
```

> [!TIP]
> You can use Helm Charts or Kustomize Templates to deploy your app or multiple ``yaml``
> config files.

> [!WARNING]
> Always check your tool version/release compatibility with your cluster version

## Managing Contexts

Kubernetes does not have the user concept so it uses the ``contexts`` to link
the cluster and the user trhrought athentication certificates exhanges

All cloud providers have their tools to allow your access to your account clusters
to accomplish that they will add a contexts in your ``~/kubectl/config`` file,
the local clusters will aolso have that behavior.

if we whant to manage our contexts our cluster by diferent file to ensure more safety
whent ruiing commands wou can use param ``--config-file ~/kubectl/<FILE-NAME>.json >``
to set this parameter as a environment varible 
``export KUBECONFIG=~/kubectl/<FILE-NAME>.json``

> [!TIP]
> you can keep your local cluster config on the default config file and your cloud
> provicer credentials on custom files

You can manage the contexts using the following command's or by using and extension/
plugin [kubectx](https://github.com/ahmetb/kubectx?tab=readme-ov-file#kubectl-plugins-macos-and-linux)

```bash
# List Contexts
kubectl config get-contexts

# Select Context
kubectl config use-context <CONTEXT NAME>

# Rename Context Alias
kubectl config rename-context <CURRENT CONTEXT NAME> <NEW CONTEXT NAME>
```

> [!TIP]
> If you have problem with your aws login profile linked with the context's, check the
> contexts config and definitions ``nano ~/.kube/config``.

It's possible to set the namespace to avoid ``-n/--namespace`` argument in every command

```bash
kubectl config set-context --current --namespace < NAMESPACE >
```

## Export to files

Its possible to export the kubernetes objects to ``json`` or ``yaml`` file, to
acomplish it use parameter ``-o`` and ``>`` to redirect stdout to a file.

kubectl get < OBJECT > -o < EXTENSION TYPE > > file.< EXTENSION TYPE >

## Reconciliation Loop

The main concept behind reconciliation loops is the notion of ``desired`` state and
``observed/current`` state. The loop is constantly running observing the current cluster
state and trying to make the current state match the desired state from the definiton
files.

## Namespaces

The namespaces are designed to provide isolation inside a cluster.

- Organization (logically grouping resources)
- Access and Resource Limit

Some resources as volumes and nodes can't be create in namespaces they are globally
avaliable whitin the cluster

The ``default`` is where you can start creating your resources.

When using custom namespaces it's always required to explicitly say wich namespace you
are accessing the namespace provide API Server isolation. If not specified the command
will be run upon the default namespace. It can be referenced as ``-n/--namespace``

> [!WARNING]
> Do not create or change namespace ``kubesystem``, this namespce is restricted to
> cluster usage.

> [!IMPORTANT]
> Resources in different namespaces are only reachble by Service routing, to achive
> this it's required Full Qualified Domain Name FQDN

## Labels

## Anotations

## Scheduler

The scheduler job is to attach Pod to Nodes, by default Kubernetes uses ``Best Effort
QoS - Quality of Service`` to share it's Pods, that means Pods will be threated with
lowest priority, it will be able to use all of the CPU and memory on the cluster and
that can lead to a cluster overhead, that's why it's important to provide ``requests``
and ``limits`` in the container definition.

    [Best Effort QoS - Quality of Service]() *default

## Selector

Selector is a filtering command to filter labels

```bash
kubectl get <RESOURCE> --selector label=<LABEL-KEY>=<DESIRED-VALUE>
```


## Deployment

Helps to manage release of new versions. The most common operations are scalling and
aplication updates

It control's ReplicaSets, teh relationship is stablished by labels and labels selector.

The rollout strategies are controlled by a deploymewnt crontroller from kubernetes it's self

replicas - this argument sets the number of container that each replicaSet shoulg handle

### Strategy

``Recreate Strategy``

``RollingUpdate Strategy``

maxSurge

maxUnavaliable

## ReplicaSets

It uses labels to select the pod to be replicated

``metadata.ownerReferences[*].name``

## DaemonSets

Help's to manage pod wich needs to be spread across cluster node's, it will be deployed
one pod per node by default, this behavior can be changed by using label selectors

## Services

``ClusterIP``

``NodePort``

``LoadBalancer``

## Secrets

```bash
kubectl get secret <SECRET> -o json | jq -r '.data | to_entries[] | "\(.key): \(.value | @base64d)"'
```

## Port Foward

Port foward creates a proxy from your local kubectl to your cluster host

```bash
kubectl port-forward service/<SERVICE-NAME> <LOCAL-PORT>:<CONTAINER-PORT> \
    --namespace <NAMESPACE>
```
SERVICE PORT will be 80 if you are connecting to services

## Internal DNS

This is how you can access your services inside your cluster whitout exposing the 
traffic to the internet

A fully qualified domain name (FQDN) is the complete domain name for a specific
computer, or host, on the internet. The FQDN consists of two parts: the hostname
and the domain name.

```bash
Headless: <SERVICE>.<NAMESPACE>.svc.cluster.local
```

## Important Concepts

### Auto Scaling

It's important to define resources and limits when working with auto scaling, so the
scheduler knows wich node has the avaliable space to allocate the pod, if no space is
left, the node auto scaling can provide new nodes also. If a pod overhead, the pod
autoscaling service can provide another pod also

Kubernetes have and ``metric-server`` < RESOURCE > in ``kube-system`` anmespace, it
monitor's Pod's and Node's resources usage

You need to set the percentage of each resource you want to reach until autoscalling is
triggered. The value is set using the ``requests`` as the basis for the calculation.

$USAGE = REAL / REQUEST$

It's possible to set multiple criteria and each one can trigger the autoscalling.

When new pods are deployed, the worload is shared between them

Checking resource usage

```bash
kubectl get hpa/application-cpu -owide
kubectl get hpa/application-memory -owide
```

To save resource usage you can set values above 100%, so one overhead will trigger it.

    Setting Requests and Limits
    The requests values should be set as close as possible to the desired operation
    values

The monitoring tools can help you to set your parameters, as CPU should be set with a
value where latency is smooth.

you can run VPA in ``recomandation`` mode to track the history of cpu and memory from
your Pod's and can recommend ypou values to be set on your definition file.

> [!IMPORTANT]
> The requess and limits are linked to the container object and it will multiply as
> Autoscaling scale the Pods

### Affinity or Anti Affinity

#### Node Affinity

#### Pod Affinity

When we talk about affinity we are just talking about deploying and running pods in a
specific node and/or next to another pod.

Pod's can have affinity or anti-affinity with other pods,it's reached by using label
selectors when we talk about affinity it's possible to rofer it to pods gravitation
where some pods tends to be closer to another,

one aplication can gravitate to another one based on affinity, by being on the same node

It indicates to the scheduler if it should provision the resources closed to another

The topology key defines the scope of our affinity, the ``hostname`` is the same node

topologyKey: "kubernetes.io/hostname" | "kubernetes.io/region"

Allocate resources based on a expression not a fixed selector, you can remove a priority rule by running ``kubectl taint nodes <NODE ID> type <LABEL KEY>:NoSchedule``

Be aware when mixin podAffinity and podAntiaffinity rules, if your cluster does not match the requirements you will get some pending pods.
