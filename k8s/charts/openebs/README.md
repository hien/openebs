------------------------------------------------------------------------------
IMPORTANT!!

DEPRECATION NOTICE:

The support for this chart will be discontinued soon. Please plan to migrate
and use stable/openebs chart located at:
 [https://github.com/helm/charts/tree/master/stable/openebs](https://github.com/helm/charts/tree/master/stable/openebs)

------------------------------------------------------------------------------

## Prerequisites

- Kubernetes 1.9.7+ with RBAC enabled
- iSCSI PV support in the underlying infrastructure
- Helm is installed and the Tiller has admin privileges. To assign admin
  to tiller, login as admin and use the following instructions:

  ```shell
  kubectl -n kube-system create sa tiller
  kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller
  kubectl -n kube-system patch deploy/tiller-deploy -p '{"spec": {"template": {"spec": {"serviceAccountName": "tiller"}}}}'
  kubectl -n kube-system patch deployment tiller-deploy -p '{"spec": {"template": {"spec": {"automountServiceAccountToken": true}}}}'
  ```

- A namespace called "openebs" is created in the Cluster for running the
  below instructions: `kubectl create namespace openebs`

## Installing OpenEBS Charts Repository

```shell
helm repo add openebs-charts https://openebs.github.io/charts/
helm repo update
helm install openebs-charts/openebs --name openebs --namespace openebs
```

## Installing OpenEBS from this codebase

```shell
git clone https://github.com/openebs/openebs.git
cd openebs/k8s/charts/openebs/
helm install --name openebs --namespace openebs .
```

## Verify that OpenEBS Volumes can be created

```shell
#Check the OpenEBS Management Pods are running.
kubectl get pods -n openebs
#Create a test PVC
kubectl apply -f https://raw.githubusercontent.com/openebs/openebs/master/k8s/demo/pvc.yaml
#Check the OpenEBS Volume Pods are created.
kubectl get pods
#Delete the test volume and associated Volume Pods.
kubectl delete -f https://raw.githubusercontent.com/openebs/openebs/master/k8s/demo/pvc.yaml

```

## Unistalling OpenEBS from Chart codebase

```shell
helm ls --all
# Note the openebs-chart-name from above command
helm del --purge <openebs-chart-name>
```

## Configuration

The following table lists the configurable parameters of OpenEBS chart and their default values.

| Parameter                               | Description                                 | Default                                           |
| ----------------------------------------| --------------------------------------------| ------------------------------------------------- |
| `rbac.create`                           | Enable RBAC Resources                       | `true`                                            |
| `image.pullPolicy`                      | Container pull policy                       | `IfNotPresent`                                    |
| `apiserver.image`                       | Docker Image for API Server                 | `quay.io/openebs/m-apiserver`                     |
| `apiserver.imageTag`                    | Docker Image Tag for API Server             | `0.8.1`                                           |
| `apiserver.replicas`                    | Number of API Server Replicas               | `1`                                               |
| `provisioner.image`                     | Docker Image for Provisioner                | `quay.io/openebs/openebs-k8s-provisioner`         |
| `provisioner.imageTag`                  | Docker Image Tag for Provisioner            | `0.8.1`                                           |
| `provisioner.replicas`                  | Number of Provisioner Replicas              | `1`                                               |
| `snapshotOperator.provisioner.image`    | Docker Image for Snapshot Provisioner       | `quay.io/openebs/snapshot-provisioner`            |
| `snapshotOperator.provisioner.imageTag` | Docker Image Tag for Snapshot Provisioner   | `0.8.1`                                           |
| `snapshotOperator.controller.image`     | Docker Image for Snapshot Controller        | `quay.io/openebs/snapshot-controller`             |
| `snapshotOperator.controller.imageTag`  | Docker Image Tag for Snapshot Controller    | `0.8.1`                                           |
| `snapshotOperator.replicas`             | Number of Snapshot Operator Replicas        | `1`                                               |
| `ndm.image`                             | Docker Image for Node Disk Manager          | `quay.io/openebs/openebs/node-disk-manager-amd64` |
| `ndm.imageTag`                          | Docker Image Tag for Node Disk Manager      | `v0.3.1`                                          |
| `ndm.sparse.enabled`                    | Create Sparse files and cStor Sparse Pool   | `true`                                            |
| `ndm.sparse.path`                       | Directory where Sparse files are created    | `/var/openebs/sparse`                             |
| `ndm.sparse.size`                       | Size of the sparse file in bytes            | `10737418240`                                     |
| `ndm.sparse.count`                      | Number of sparse files to be created        | `1`                                               |
| `ndm.sparse.filters.excludeVendors`     | Exclude devices with specified vendor       | `CLOUDBYT,OpenEBS`                                |
| `ndm.sparse.filters.excludePaths`       | Exclude devices with specified path patterns| `loop,fd0,sr0,/dev/ram,/dev/dm-`                  |
| `jiva.image`                            | Docker Image for Jiva                       | `quay.io/openebs/jiva`                            |
| `jiva.imageTag`                         | Docker Image Tag for Jiva                   | `0.8.1`                                           |
| `jiva.replicas`                         | Number of Jiva Replicas                     | `3`                                               |
| `cstor.pool.image`                      | Docker Image for cStor Pool                 | `quay.io/openebs/cstor-pool`                      |
| `cstor.pool.imageTag`                   | Docker Image Tag for cStor Pool             | `0.8.1`                                           |
| `cstor.poolMgmt.image`                  | Docker Image for cStor Pool  Management     | `quay.io/openebs/cstor-pool-mgmt`                 |
| `cstor.poolMgmt.imageTag`               | Docker Image Tag for cStor Pool Management  | `0.8.1`                                           |
| `cstor.target.image`                    | Docker Image for cStor Target               | `quay.io/openebs/cstor-istgt`                     |
| `cstor.target.imageTag`                 | Docker Image Tag for cStor Target           | `0.8.1`                                           |
| `cstor.volumeMgmt.image`                | Docker Image for cStor Volume  Management   | `quay.io/openebs/cstor-volume-mgmt`               |
| `cstor.volumeMgmt.imageTag`             | Docker Image Tag for cStor Volume Management| `0.8.1`                                           |
| `policies.monitoring.image`             | Docker Image for Prometheus Exporter        | `quay.io/openebs/m-exporter`                      |
| `policies.monitoring.imageTag`          | Docker Image Tag for Prometheus Exporter    | `0.8.1`                                           |
| `analytics.enabled`                     | Enable sending stats to Google Analytics    | `true`                                            |
| `analytics.pingInterval`                | Duration(hours) between sending ping stat   | `24h`                                             |
| `HealthCheck.initialDelaySeconds`       | Delay before liveness probe is initiated    | `30`                                              |                              | 30                                                          |
| `HealthCheck.periodSeconds`             | How often to perform the liveness probe     | `60`                                              |                            | 10                                                          |

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`.

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart. For example,

```shell
helm install --name openebs -f values.yaml openebs-charts/openebs
```

> **Tip**: You can use the default [values.yaml](values.yaml)
