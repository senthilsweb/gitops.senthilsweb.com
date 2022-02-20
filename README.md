# gitops.senthilsweb.com

Repository to keep my public containers and kubernetes assets following gitops principles

## About the application

- KinD is used to setup local Kubernetes cluster with 1 Master and 2 Worker Nodes
- A simple static website hosted in nginx server pods with 2 replicaset, the source code folder `/usr/share/nginx/html` of nginx is mounted to the host filesystem using kubernetes `PersistentVolume` and `PersistentVolumeClaim`.
- External access of the application is enabled thru Kubernetes `service` and `port-forwarding`

## Installation

Create local kubernetes cluster with `extra mount` for the worker nodes

```
kind create cluster --name zypress-cluster --config kind-cluster.yaml
```

Deploy the application. Whenever you make any changes to the `deployment`, you should reconfigure the service as the pods will be terminated and recreated making the `port-forward` disabled

```
#PersistentVolume
kubectl apply -f pv.yaml

#PersistentVolumeCliam
kubectl apply -f pvc.yaml

#Deploy
kubectl apply -f deployment.yaml

#Service
kubectl apply -f service.yaml
```

Port Forward localhost:80 to containerport:80

```
sudo kubectl port-forward svc/nginx-http 80:80
sudo kubectl port-forward svc/notifier-api 4000:3000
```

### Miscellaneous Commands

```
kubectl cluster-info

kubectl -n argocd get all

kubectl -n argocd edit svc argocd-server

# Check the nodes with kubectl
kubectl get nodes -o wide

# Check the namespace
kubectl get namespaces

# Get pods
kubectl get pods -n [namespace]

kubectl get pv
kubectl get pvc
kubectl get all -o wide
kubectl get po -o wide
kind delete cluster --name zypress-cluster
kubectl patch ns metallb-system -p '{"metadata":{"finalizers":null}}'
kubectl get namespace metallb-system -o json > tmp.json

```

## References

- [how-to-reference-a-local-volume-in-kind-kubernetes-in-docker](https://stackoverflow.com/questions/62694361/how-to-reference-a-local-volume-in-kind-kubernetes-in-docker)

kubectl -n argocd port-forward service/argocd-server 8080:80
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo

kubectl Port Forward localhost:4000 to containerport:3000
