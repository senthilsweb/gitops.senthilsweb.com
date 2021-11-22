#Introduction


## Create Kind Cluster
```
# Create a kubernetes cluster of 1 control plane and 2 worker nodes
kind create cluster --name zypress-cluster --config kind-cluster.yaml

# Check the cluster info
kubectl cluster-info -n zypress-cluster

# Check the nodes with kubectl
kubectl get nodes -o wide
```

## Install Argocd

```
# Create namespace

kubectl create namespace argocd

# Install argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Get pods status
get pods -n argocd

# Port forwad to access argocd frontend
kubectl -n argocd port-forward service/argocd-server 8080:80

# Get default password to access argocd webpanel
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo

#
kubectl get pods -n zypress-app

# Port forward web application
kubectl port-forward svc/notifier-api-service 3000:80
```

## Cluster commands

The initial password for the admin account is auto-generated and stored as clear text in the field password in a secret named argocd-initial-admin-secret in your Argo CD installation namespace. You can simply retrieve this password using kubectl:

```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```

Port forwad to access argocd frontend

```
kubectl -n argocd port-forward service/argocd-server 8080:80
```



## Application commands



Port forwad to access api server

```
sudo kubectl port-forward svc/notifier-api-service 4000:3000
```


## References

* [Argocd docs](https://github.com/argoproj/argo-cd/blob/master/docs/getting_started.md)
* [Deployment of multiple apps on Kubernetes cluster](https://wkrzywiec.medium.com/deployment-of-multiple-apps-on-kubernetes-cluster-walkthrough-e05d37ed63d1)