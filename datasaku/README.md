# Instalation

It will be a deployment in kubernetes without service (just sleep infinitely)

## Build Docker
```
eval $(minikube -p datasaku-cluster docker-env)
docker build --pull --no-cache  -f ./Dockerfile -t datasaku:1.0.0 .
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

# setup environment

## git pull
```
git clone https://github.com/kurangdoa/datasaku.git
git clone https://github.com/kurangdoa/sakuflow.git
```

## Troubleshoot
```
kubectl get pv -n datasaku-dev
kubectl get pvc -n datasaku-dev
kubectl exec -it pod/datasaku-dev -n datasaku-dev -- /bin/bash
kubectl describe pod/datasaku-dev -n datasaku-dev
kubectl logs pod/datasaku-dev -n datasaku-dev
docker run -it datasaku:1.0.0 /bin/bash
sudo cat /etc/sudoers.d/99-chowndata
```

# Optional

## Mount Local Path to Pod
```
minikube mount /Users/rhyando/code/development/datasaku/data:/data/datasaku -p datasaku-cluster
```