## Install K3s Kubernetes cluster

Install K3s with execute permission

```
curl -sfL https://get.k3s.io | sh -s - --write-kubeconfig-mode 644
```

```
systemctl status k3s.service

export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
sudo chmod 644 /etc/rancher/k3s/k3s.yaml


## UFW firewall rules
ufw allow 22/tcp
ufw allow 80/tcp
ufw allow 443/tcp
ufw allow 6443/tcp
ufw allow 2222/tcp
ufw enable
systemctl enable --now ufw
```

## Install Kubectl for arm64

```
curl -LO "https://dl.k8s.io/release/v1.24.0/bin/darwin/arm64/kubectl"
chmod +x ./kubectl
mv ./kubectl /usr/local/bin/kubectl
export PATH=$PATH:/usr/local/git/bin:/usr/local/bin
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

kubectl patch svc service/traefik -n kube-system -p '{"spec": {"type": "LoadBalancer", "externalIPs":["132.145.165.107"]}}'

Restart the `K3s` and redeploy `traefik` for the changes to take effect

```
sudo systemctl restart k3s
kubectl -n kube-system scale deploy traefik --replicas 0
kubectl -n kube-system scale deploy traefik --replicas 1
```

Access `Traefik Dashboard` at `http://[ip-address]:9000/dashboard/`

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
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.4.0-rc1/manifests/install.yaml
```

Change ArgoCD server as `LoadBalancer`

```
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer", "externalIPs":["132.145.165.107"]}}'
```

### Access ArgoCD dashboard

Access dashboard at `https://[ip-address]:32464/`

kubectl get all -n argocd

Find out the http port for `service/argocd-server` then login as `admin' and password using below

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo

## Optional if you get error as nginx webhook

kubectl delete -A ValidatingWebhookConfiguration ingress-nginx-admission

## Optional - Creating SSL Certificate using Letsencrypt

```
kubectl create namespace cert-manager

curl -sL  https://github.com/cert-manager/cert-manager/releases/download/v1.8.0/cert-manager.yaml | sed -r 's/(image:.*):(v.*)$/\1-arm:\2/g' > cert-manager-arm.yaml

kubectl apply --validate=false -f cert-manager-arm.yaml

kubectl --namespace cert-manager get pods
```

sudo nano letsencrypt-issuer-production.yaml

```
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-production
  namespace: default
spec:
  acme:
    # The ACME server URL
    server: https://acme-v02.api.letsencrypt.org/directory
    # Email address used for ACME registration
    email: senthilsweb@gmail.com
    # Name of a secret used to store the ACME account private key
    privateKeySecretRef:
      name: letsencrypt-production
    # Enable the HTTP-01 challenge provider
    solvers:
    - http01:
        ingress:
          ingressTemplate:
            metadata:
              annotations:
                kubernetes.io/ingress.class: traefik
```

```
kubectl apply -f ./letsencrypt-issuer-production.yaml
```

### Miscellaneous (Not required)

```
sudo kubectl port-forward pod/traefik-7f9778594c-cx2sk -n kube-system 80:80 --address 0.0.0.0
```

# Reference

- https://issuemode.com/issues/argoproj/argo-cd/86332687

- https://stackoverflow.com/questions/68565048/how-to-expose-traefik-v2-dashboard-in-k3d-k3s-via-configuration

- https://carpie.net/articles/installing-and-using-cert-manager-with-k3s
