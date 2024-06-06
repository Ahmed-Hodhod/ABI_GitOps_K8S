### Install Kubectl, Eksctl, aws utilities 

### Install Docker before Minikube 
```
https://docs.docker.com/engine/install/ubuntu/
```

## Install Minikube  and configuring it to run the dashboard on a remote machine 
``` 
https://medium.com/@areesmoon/installing-minikube-on-ubuntu-20-04-lts-focal-fossa-b10fad9d0511
https://medium.com/@areesmoon/setting-up-minikube-and-accessing-minikube-dashboard-09b42fa25fb6

```


### Start Minikube 
```
minikube start — driver=docker
minikube kubectl -- get pods -A
minikube status
minikube kubectl -- cluster-info 
minikube addons list
minikube addons list
minikube addons enable ingress
minikube dashboard (open it in the browser to have a view of your cluster resources as in eks)

```




