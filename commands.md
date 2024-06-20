## Install Docker before Minikube 
```
https://docs.docker.com/engine/install/ubuntu/
```

## Install Minikube and configure it to run the dashboard on a remote machine 
``` 
https://medium.com/@areesmoon/installing-minikube-on-ubuntu-20-04-lts-focal-fossa-b10fad9d0511
https://medium.com/@areesmoon/setting-up-minikube-and-accessing-minikube-dashboard-09b42fa25fb6

```

## Minikube Commands 
```
minikube start — driver=docker
minikube status
minikube kubectl -- cluster-info 
minikube addons list
minikube addons list
minikube addons enable ingress
minikube dashboard (open it in the browser to have a view of your cluster resources as in eks)

```

## Docker Commands
```
docker tag 
docker push ahmedhodhod1/wordpress:tagname

docker build -t my-wordpress .
docker run -e WORDPRESS_DB_HOST=db:3306 \
           -e WORDPRESS_DB_USER=wordpress \
           -e WORDPRESS_DB_PASSWORD=wordpress \
           -e WORDPRESS_DB_NAME=wordpress \
           -p 8080:80 my-wordpress
docker-compose up -d

```

## ArgoCD Commands
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

VERSION=$(curl --silent "https://api.github.com/repos/argoproj/argo-cd/releases/latest" | grep '"tag_name":' | sed -E 's/.*"([^"]+)".*/\1/')
curl -sSL -o argocd-linux-amd64 "https://github.com/argoproj/argo-cd/releases/download/$VERSION/argocd-linux-amd64"
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd

argocd repo add https://github.com/Ahmed-Hodhod/ABI_GitOps_K8S --username Ahmed-Hodhod --password github_pat_11AO7M4II0U7H82uy6u7BY_1q7eULvhMQulRsPAUnksYzYCXWuTI44JAE74KSLguvk5kvb
argocd login localhost:8080
```

## Run ArgoCD Operator
```
kubectl port-forward svc/argocd-server -n argocd 8080:443
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo

```



## HELM 
```
helm create app
helm install app ./app
helm upgrade app app/ --values app/values.yaml
```


## Authenticate to DockerHub

```
docker login -u ahmedhodhod1
cat ~/.docker/config.json
cat ~/.docker/config.json | base64 -w0   
```

## Create a secret based off dockerhub credentials 

```
kubectl create secret generic regcred \
    --from-file=.dockerconfigjson=/home/codespace/.docker/config.json \
    --type=kubernetes.io/dockerconfigjson

kubectl get secret regcred --output=yaml
```


## Run the Wordpress Application

```
kubectl port-forward svc/wordpress 8000:80
```