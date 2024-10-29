# Digital Ocean

## Create Kubernetes Cluster

### List Kubernetes Options Sizes

```shell
doctl kubernetes options sizes
```

### Create K8s Cluster

```shell
doctl kubernetes cluster create devops --node-pool "name=devops-pool;size=s-1vcpu-2gb;count=2"
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