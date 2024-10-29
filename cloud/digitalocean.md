# Digital Ocean

## Create Kubernetes Cluster

### List Kubernetes Options Sizes

```shell
doctl kubernetes options sizes
```

### Create K8s Cluster

```shell
doctl kubernetes cluster create devops --size s-2vcpu-4gb
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