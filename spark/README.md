# Instalation
Based on https://github.com/testdrivenio/spark-kubernetes

## Build Docker Images in Minikube VM
```
eval $(minikube -p datasaku-cluster docker-env)
docker build -f ./docker/Dockerfile -t spark-hadoop:3.5.1 ./docker
```
## Delete Resource and Create Namespace
```
kubectl delete namespace spark-dev
kubectl delete -f ./kubernetes/rbac.yaml -n spark-dev
kubectl create namespace spark-dev
```

## Spark Deployment
```
kubectl create -f ./kubernetes/spark-master-deployment.yaml -n spark-dev
kubectl create -f ./kubernetes/spark-master-service.yaml -n spark-dev 
kubectl create -f ./kubernetes/spark-worker-deployment.yaml -n spark-dev
kubectl create -f ./kubernetes/rbac.yaml -n spark-dev
kubectl create -f ./kubernetes/spark-ingress.yaml -n spark-dev
kubectl create -f ./kubernetes/spark-worker-service.yaml -n spark-dev 
```

To use DNS in your local, execute
```
echo "127.0.0.1 spark.kubernetes.net" | sudo tee -a /etc/hosts
or echo "$(minikube -p spark-cluster ip) spark-kubernetes" | sudo tee -a /etc/hosts
```


## Deploy Spark UI Proxy
based on https://github.com/aseigneurin/spark-ui-proxy

### Spark Proxy Docker in Minikube VM
```
eval $(minikube -p datasaku-cluster docker-env)
docker build -f ./proxy/Dockerfile -t spark-proxy:1.0.0 ./proxy
```

### Deploy Spark Proxy
```
kubectl create -f ./proxy/spark-ui-deployment.yaml -n spark-dev
kubectl create -f ./proxy/spark-ui-service.yaml -n spark-dev
```

## check deployment
```
kubectl get svc -n spark-dev
kubectl get pods -n spark-dev -o wide
kubectl get deployments -n spark-dev
kubectl get pod -o wide -n spark-dev
kubectl get all -n spark-dev
```
# Access
To access the spark UI, enter this URL below
http://127.0.0.1:7887/proxy:spark-master:7890  
or  
http://spark.kubernetes.net:7890/ui

# Troubleshoot
If worker is not running correctly, check minikube tunnel and enter your sudo password again