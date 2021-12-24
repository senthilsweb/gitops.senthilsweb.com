# Introduction

Zyfra gitops based deployment procedure using Kind kubernetes cluster with traefik ingress & ArgoCD

## Deployment Instructions

* Clone repository
* Change directory into `cd gitops.senthilsweb.com`
* Create Kind Cluster
```
# Create a kubernetes cluster with 1control plane and 2 worker nodes
kind create cluster  --config kind-cluster-zyfra.yaml

# Check the cluster info
kubectl cluster-info -n zyfra-cluster

# Check the nodes with kubectl
kubectl get nodes -o wide
```
* Deploy Argocd

```
# Create namespace
kubectl create namespace argocd

# Install argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Get pods status (wait for 3 ~ 5 mins depending on your internet)
kubectl get pods -n argocd

# Port forwad to access argocd frontend
kubectl -n argocd port-forward service/argocd-server 8080:80

# Get default password to access argocd webpanel
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```
* Deploy traefik ingress controller (wait for 1 ~ 3 mins depending on your internet)

```
helm repo add traefik https://helm.traefik.io/traefik
helm repo update
helm install traefik traefik/traefik -n traefik --create-namespace -f traefik.values.yaml

# Get traefik pods status
kubectl get pods -n traefik  -o wide
```

* Access [Traefik dashboard](https://localhost:8080)

* Install zyfra app from AgroCD Web GUI [Argocd dashboard](http://127.0.0.1:9000/dashboard/)

![App Creation](https://res.cloudinary.com/nathansweb/image/upload/v1640230282/ArgoCD/argocd_app_create.png)

* Access the application

Request |       Endpoints                                                |       Memo
--------|----------------------------------------------------------------|--------------------------------
WEB     |  http://localhost:8083/                                        |   Simple vue.js web application 
API     |  http://localhost:8081/rest/v1/__health                        |   Healthcheck
API     |  http://localhost:8081/rest/v1/contacts                        |   List contacts


## Miscellaneous

```
# Uninstall traefik
helm uninstall traefik  -n traefik

# Upgrade traefik
helm upgrade traefik traefik/traefik -n traefik --create-namespace -f traefik.values.yaml

#
kubectl get all -n zyfra  -o wide

#
kind delete cluster --name zyfra-cluster

#
kubectl get pods -n zyfra
```

## References

* [Argocd docs](https://github.com/argoproj/argo-cd/blob/master/docs/getting_started.md)
* [k8ssandra](https://docs.k8ssandra.io/tasks/connect/ingress/kind-deployment/)
* [Deployment of multiple apps on Kubernetes cluster](https://wkrzywiec.medium.com/deployment-of-multiple-apps-on-kubernetes-cluster-walkthrough-e05d37ed63d1)