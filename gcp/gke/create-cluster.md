# Create GKE Cluster

## List Available Versions

```shell
gcloud container get-server-config --region us-central1
```

## Create GKE Cluster (7 minutes)

```shell
gcloud container clusters create "gke-lab" \
    --cluster-version "1.30.5-gke.1699000" --num-nodes 3 --zone "us-central1-a" \
    --machine-type "e2-medium" --release-channel=stable --network "default" \
    --subnetwork "default" \
    --enable-ip-alias \
    --enable-autorepair \
    --enable-autoupgrade \
    --workload-pool "development-373903.svc.id.goog"
```

## List GKE Cluster

```shell
gcloud container clusters list
NAME     LOCATION       MASTER_VERSION      MASTER_IP      MACHINE_TYPE  NODE_VERSION        NUM_NODES  STATUS
gke-lab  us-central1-a  1.30.5-gke.1699000  35.222.73.128  e2-medium     1.30.5-gke.1699000  3          RUNNING
```

## List Current Context

```shell
kubectl config current-context
gke_development-373903_us-central1-a_gke-lab
```

## List GKE Nodes

```shell
kubectl get nodes
NAME                                     STATUS   ROLES    AGE    VERSION
gke-gke-lab-default-pool-a88a9874-8v9z   Ready    <none>   3m5s   v1.30.5-gke.1699000
gke-gke-lab-default-pool-a88a9874-l2wt   Ready    <none>   3m4s   v1.30.5-gke.1699000
gke-gke-lab-default-pool-a88a9874-lq3n   Ready    <none>   3m5s   v1.30.5-gke.1699000
```

## Delete GKE Cluster (5 minutes)

```shell
gcloud container clusters delete gke-lab
The following clusters will be deleted.
 - [gke-lab] in [us-central1-a]

Do you want to continue (Y/n)? Y
Deleting cluster gke-lab...
```

## Validate GKE Cluster Delete

```shell
gcloud container clusters list
```