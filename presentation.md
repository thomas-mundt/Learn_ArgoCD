# ArgoCD Presentation

## Setup Kubernetes Cluster on Hetzner with Kubespray

Prerequisites:
- Create 2 Nodes with 2vCpu and 2 GB ram 


```
git clone https://github.com/kubernetes-sigs/kubespray.git
cd kubespray

virtualenv venv
source venv/bin/activate

pip3 install -r requirements.txt

```

```
cp -rfp inventory/sample inventory/mycluster
```



## Update Ansible inventory file with inventory builder
```
declare -a IPS=(10.10.1.3 10.10.1.4)
CONFIG_FILE=inventory/mycluster/hosts.yaml python3 contrib/inventory_builder/inventory.py ${IPS[@]}
```


## Review and change parameters
```
vim ./inventory/group_vars/k8s-cluster.yml
-kube_network_plugin: calico
+kube_network_plugin: flannel
```


Create the cluster
```
ansible-playbook -i inventory/mycluster/hosts.yaml  --user root cluster.yml
```


Install Helm
```
curl https://baltocdn.com/helm/signing.asc | sudo apt-key add - && \
sudo apt-get install apt-transport-https --yes && \
echo "deb https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list && \
sudo apt-get update && \
sudo apt-get install helm
```


Install Ingress-controller
```
kubectl create ns nginx-controller && \
helm repo add nginx-stable https://helm.nginx.com/stable && \
helm repo update && \
helm install nginx nginx-stable/nginx-ingress -n nginx-controller
```


## Install ArgoCD

Prerequisites:
- Minimum 4vcpu and 8GB Ram  

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Change service argocd-server to NodePort
```
k edit svc argocd-server -n argocd
k get svc argocd-server -n argocd
k get nodes -o wide
```

Login
```
User: admin
Password: Name of argocd-server pod
k get po -n argocd
```



## First Application from Git

New App =>  
https://github.com/thomas-mundt/Learn_ArgoCD  
yamls  

Points to describe:  
- Details  
- Out of sync  
- Manual sync  
- App Diff  

Sync  
- Show resources  
- Edit service manually to ClusterIP/NodePort  
- Edit deployment in Git  
- History and Rollback  

Helm (Add in Gui)  
- jaegertracing       	https://jaegertracing.github.io/helm-charts  
- stable              	https://charts.helm.sh/stable  
- bitnami             	https://charts.bitnami.com/bitnami  
- prometheus-community	https://prometheus-community.github.io/helm-charts  
- grafana             	https://grafana.github.io/helm-charts  

Add new App with Helm  
- grafana  
- Edit service to NodePort  
- Show Grafana Gui  
- Change to NodePort in Gui under App Details  
- Change to older Grafana version number  
- Enable Autosync + Prune + Selfheal  
- Edit deployment to 10 replicas  
- Explain benefit of unexpected deletion of ressources  



## First Helm Application from Git

- Add Repo: https://github.com/thomas-mundt/Learn_ArgoCD.git  
- Create Application (Helm Charts will automatically be scanned)  





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


## Add a cluster

```
kubectl config get-contexts -o name
argocd cluster add docker-desktop
```

```
kubectl config get-contexts
kubectl config rename-context <name> <new-name>
```




