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

#
kubectl get all -n zyfra  -o wide

#
kubectl get pods -n zyfra

# Port forward web application
kubectl port-forward -n zyfra svc/notifier-web-service 3000:3000

# Port forward api server
kubectl port-forward -n zyfra svc/notifier-api-service 5000:3000

# ssh into pod
kubectl -n zyfra exec -it notifier-web-server-5cd6cb9995-4fjht -- sh


# metallb install by manifest [install by manifest](https://metallb.universe.tf/installation/)
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.11.0/manifests/namespace.yaml
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.11.0/manifests/metallb.yaml

# Find the address pool to be used by loadbalancers
docker network inspect -f '{{.IPAM.Config}}' kind

# Metallb configuration using Layer 2 Configuration 
kubectl create -f /pathto/metallb-configmap.yaml

kubectl patch svc <svc-name> -n <namespace> -p '{"spec": {"type": "LoadBalancer"}}

```

### Miscellaneous

Use the `service-name`:`port` to connect with the service

```
#install curl
apk --no-cache add curl

#List all ports in use
lsof -i -P | grep LISTEN

# Kill a port
kill -9 <pid>

```

## Install `curl`

```
apk --no-cache add curl
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