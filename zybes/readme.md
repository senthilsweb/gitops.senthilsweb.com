## Install K3s Kubernetes cluster

Install K3s with execute permission

```
curl -sfL https://get.k3s.io | sh -s - --write-kubeconfig-mode 644
```

Enable Traefik Dashboard by creating custom `traefik-config.yaml` file as below

```
nano /var/lib/rancher/k3s/server/manifests/traefik-config.yaml
```

`traefik-config.yaml`

```
apiVersion: helm.cattle.io/v1
kind: HelmChartConfig
metadata:
  name: traefik
  namespace: kube-system
spec:
  valuesContent: |-
    dashboard:
      enabled: true
    ports:
      traefik:
        expose: true # this is not recommended in production deployments, but I want to be able to see my dashboard locally
    logs:
      access:
        enabled: true
```

Restart the `K3s` and redeploy `traefik` for the changes to take effect

```
sudo systemctl restart k3s
kubectl -n kube-system scale deploy traefik --replicas 0
kubectl -n kube-system scale deploy traefik --replicas 1
```

Un-install k3s

```
/usr/local/bin/k3s-uninstall.sh
```

## Install ArgoCD (ARM64 compatible version to run in Raspberry pi)

```
kubectl create namespace argocd
```

```
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/release-2.4/manifests/install.yaml
```

```
kubectl apply -n argocd -f https://github.com/argoproj/argo-cd/blob/v2.4.0-rc1/manifests/install.yaml
```

Change ArgoCD server as `LoadBalancer`

```
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```

### Access ArgoCD dashboard

kubectl get all -n argocd

Find out the http port for `service/argocd-server` then login as `admin' and password using below

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo

## Optional if you get error as nginx webhook

kubectl delete -A ValidatingWebhookConfiguration ingress-nginx-admission

# Reference

https://issuemode.com/issues/argoproj/argo-cd/86332687
