# lakehouse
In pursue of building local modern data warehouse 
that will be easily migrate over to cloud.

## Pre-requisites

### Environment
- Apple M1 Pro
- Sonoma 14.5
- Python 3.10.9

### Other Tools
- docker [link](https://www.docker.com/products/docker-desktop/)
- homebrew [link](https://brew.sh/)
- minikube [link](https://minikube.sigs.k8s.io/docs/start/?arch=%2Fmacos%2Fx86-64%2Fstable%2Fhomebrew)
- kubectl [link](https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/)

## Create Minikube Cluster
create cluster for the whole project
```
minikube delete -p datasaku-cluster 
minikube start -p datasaku-cluster --disk-size 20000mb --driver docker --memory=max --cpus=max
```

## Install add-ons
```
minikube addons -p datasaku-cluster enable ingress 
minikube addons -p datasaku-cluster enable ingress-dns
minikube addons -p datasaku-cluster enable storage-provisioner
```

## Create Tunnel
```
minikube tunnel -p datasaku-cluster
```