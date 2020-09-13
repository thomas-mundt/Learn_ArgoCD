# Learn ArgoCD

Install
```
minikube start

kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

brew install argocd

kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
or ??
kubectl port-forward svc/argocd-server -n argocd 8080:443


kubectl get pods -n argocd -l app.kubernetes.io/name=argocd-server -o name | cut -d'/' -f 2

minikube ip

#Username: admin
argocd login <ARGOCD_SERVER>

argocd account update-password

argocd cluster add

argocd cluster add minikube



```




## Create An Application From A Git Repository

```
https://github.com/argoproj/argocd-example-apps.git

```


