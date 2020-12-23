# Learn ArgoCD

## Install With Helm


Minimum 4vcpu and 8GB Ram

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

```

- Change service argocd-server to NodePort

Login
```
User: admin
Password: Name of argocd-server pod
```


Add Github URL
```
https://github.com/thomas-mundt/Learn_ArgoCD
```



## Install In Minikube

```
minikube start

kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

brew install argocd


kubectl port-forward svc/argocd-server -n argocd 8080:443

# Get password
kubectl get pods -n argocd -l app.kubernetes.io/name=argocd-server -o name | cut -d'/' -f 2

minikube ip

#Username: admin
argocd login <ARGOCD_SERVER>
or
argocd login localhost:8080 --insecure --username admin --password "${ARGOCD_PASS}"

argocd account update-password

argocd cluster add

argocd cluster add minikube


```




## Create An Application From A Git Repository

```
https://github.com/argoproj/argocd-example-apps.git

```


## Test

Shows outofsync in ArgoCD
```
kubectl scale deploy nginx --replicas 2
```

- Enable AutoSync
- Prune
- Self Healing

- Roleback



## Use Helm Templates

```
helm repo list
>stable	https://charts.helm.sh/stable
```

- Use Grafana and version older than recent
- Later change to NodePort
- Later set password
- Update Chart Version to latest

- k get applications -n argocd


## Declarative Application (Custon Ressource)

```
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: guestbook
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/argoproj/argocd-example-apps.git
    targetRevision: HEAD
    path: guestbook
  destination:
    server: https://kubernetes.default.svc
    namespace: guestbook
```

