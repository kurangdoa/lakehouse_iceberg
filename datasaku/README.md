# Instalation

It will be a deployment in kubernetes without service (just sleep infinitely)

## Build Docker
```
eval $(minikube -p datasaku-cluster docker-env)
docker build -f ./Dockerfile -t datasaku:1.0.0 .
```

## Mount Local Path to Pod
```
minikube mount /Users/rhyando/code/development/datasaku/data:/data/datasaku -p datasaku-cluster
```

## Create Namespace
```
kubectl delete namespace datasaku-dev
kubectl create namespace datasaku-dev
```

## Create PV and PVC
```
kubectl delete pv datasaku-volume
kubectl apply -n datasaku-dev -f datasaku-pv.yaml
kubectl apply -n datasaku-dev -f datasaku-pvclaim.yaml
```

## Deployment
```
kubectl apply -n datasaku-dev -f datasaku-deployment.yaml
kubectl get all -n datasaku-dev
```

## Troubleshoot
```
kubectl get pv -n datasaku-dev
kubectl get pvc -n datasaku-dev
```