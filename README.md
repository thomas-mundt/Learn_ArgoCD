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
  namespace: argocd   # Have to be argocd
spec:
  project: default
  source:
    repoURL: https://github.com/thomas-mundt/Learn_ArgoCD
    targetRevision: HEAD
    path: yamls
  destination:
    server: https://kubernetes.default.svc
    namespace: default
```
k apply -f <file>
k get applications -n argocd


## Kustomize

kustomize.yaml
```
# Add
commonAnnotations:
  channel: devopszone
```


## Argo CD CLI

```
VERSION=$(curl --silent "https://api.github.com/repos/argoproj/argo-cd/releases/latest" | grep '"tag_name"' | sed -E 's/.*"([^"]+)".*/\1/')
curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/download/$VERSION/argocd-linux-amd64

chmod +x /usr/local/bin/argocd

argocd help

argocd login https://168.119.238.148:31771
>y
>admin
>Name off argocd pod

#Or
argocd login 168.119.238.148:31771 --insecure

argocd proj list
argocd repo list
argocd app list
argocd logout 168.119.238.148:31771
argocd login 168.119.238.148:31771 --insecure --username admin --password <Name off argocd pod>

argocd proj create myproj
```

## Set Password

Can only be done in the cli
```
argocd account help
argocd account get-user-info
argocd account get admin
argocd account update-password


```


## Create new Application

```
argocd app create  --help | less

argocd app create guestbook --repo https://github.com/argoproj/argocd-example-apps.git --path guestbook --dest-namespace default --dest-server https://kubernetes.default.svc --directory-recurse

argocd app sync guestbook
argocd app get guestbook
argocd app list
argocd app resources guestbook
argocd app delete guestbook


```



