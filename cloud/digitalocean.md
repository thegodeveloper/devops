# Digital Ocean

## Create Kubernetes Cluster

### List Kubernetes Options Sizes

```shell
doctl kubernetes options sizes
```

### List Kubernetes Versions

```shell
doctl kubernetes options versions
```

### Create K8s Cluster

```shell
doctl kubernetes cluster create devops --version 1.30.5-do.3 --node-pool "name=devops-pool;size=s-2vcpu-4gb;count=2"
```

### Create Small Cluster

```shell
doctl kubernetes cluster create devops --version 1.31.1-do.3 --node-pool "name=devops-pool;size=s-2vcpu-2gb;count=2"
```

## Delete the Kubernetes Cluster

```shell
doctl kubernetes cluster delete devops
```

## List Kubernetes Cluster

```shell
doctl kubernetes cluster list
ID    Name    Region    Version    Auto Upgrade    Status    Node Pools
```

## Save Kubernetes Cluster Kubeconfig

```shell
doctl kubernetes cluster kubeconfig save devops
```

