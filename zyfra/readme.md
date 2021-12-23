#Introduction

## Create Kind Cluster
```
# Create a kubernetes cluster with 1control plane and 2 worker nodes
kind create cluster  --config kind-cluster-zyfra.yaml

# Check the cluster info
kubectl cluster-info -n zyfra-cluster

# Check the nodes with kubectl
kubectl get nodes -o wide
```

## Install Argocd

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

# Ingress NGINX
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
# Get nginx pods status (wait for 3 ~ 5 mins depending on your internet)
kubectl get pods -n ingress-nginx

```

### Install zyfra app from Agro Web GUI

![App Creation](https://res.cloudinary.com/nathansweb/image/upload/v1640230282/ArgoCD/argocd_app_create.png)


### Miscellaneous

```
#
kind delete cluster --name zyfra-cluster
#
kubectl get all -n zyfra  -o wide
#
kubectl get pods -n zyfra
```

## References

* [Argocd docs](https://github.com/argoproj/argo-cd/blob/master/docs/getting_started.md)
* [Deployment of multiple apps on Kubernetes cluster](https://wkrzywiec.medium.com/deployment-of-multiple-apps-on-kubernetes-cluster-walkthrough-e05d37ed63d1)