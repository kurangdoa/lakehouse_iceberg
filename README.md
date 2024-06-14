# lakehouse

On the miss

## Pre-requisites
Instalation of minikube 

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