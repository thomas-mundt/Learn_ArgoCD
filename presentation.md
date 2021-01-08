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
curl https://baltocdn.com/helm/signing.asc | sudo apt-key add -
sudo apt-get install apt-transport-https --yes
echo "deb https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```


Install Ingress-controller
```
kubectl create ns nginx-controller
helm repo add nginx-stable https://helm.nginx.com/stable
helm repo update

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


Add Github URL: https://github.com/thomas-mundt/Learn_ArgoCD

