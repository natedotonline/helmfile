# helmfile

## Using

### Prereqs

Ensure you have [Mise](https://mise.jdx.dev) and [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed.

### Tool Installation

Clone this repo to your local machine first.

```shell
mise install
```

### Creating Test Clusters

```shell
kind create cluster --name kind1
kubectl config use-context kind-kind1
kubectl create ns nginx

kind create cluster --name kind2
kubectl config use-context kind-kind2
kubectl create ns nginx
```

### Deploying & Verifying

```shell
helmfile --environment kind1 sync
```

```shell
helmfile --environment kind2 sync
```

### Testing

#### 'kind1' Cluster

```shell
kubectl config use-context kind-kind1
export POD_NAME=$(kubectl get pods --namespace nginx -l "app.kubernetes.io/name=static-site,app.kubernetes.io/instance=static-site" -o jsonpath="{.items[0].metadata.name}")
export CONTAINER_PORT=$(kubectl get pod --namespace nginx $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
kubectl --namespace nginx port-forward $POD_NAME 8081:$CONTAINER_PORT
```

Browse to [http://localhost:8081/](http://localhost:8081/) and you should see a page saying `This is kind1.`. This values comes from [kind1/index.html](kind1/index.html), which is configured in [values/values.kind1.yaml](values/values.kind1.yaml).

Terminate the port forwarding via `Ctrl+C` in your shell.

#### 'kind2' Cluster

```shell
kubectl config use-context kind-kind2
export POD_NAME=$(kubectl get pods --namespace nginx -l "app.kubernetes.io/name=static-site,app.kubernetes.io/instance=static-site" -o jsonpath="{.items[0].metadata.name}")
export CONTAINER_PORT=$(kubectl get pod --namespace nginx $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
kubectl --namespace nginx port-forward $POD_NAME 8082:$CONTAINER_PORT
```

Browse to [http://localhost:8082/](http://localhost:8082/) and you should see a page saying `This is kind2.`. This values comes from [kind2/index.html](kind1/index.html), which is configured in [values/values.kind2.yaml](values/values.kind2.yaml).

Terminate the port forwarding via `Ctrl+C` in your shell.
