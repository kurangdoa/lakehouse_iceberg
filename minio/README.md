# minio
The instalation of minio with statefulset is based on https://github.com/minio/minio/issues/6775

## Create a Namespace
Create the namespace
```
kubectl delete namespace minio-dev
kubectl create namespace minio-dev
```

## Create PV and PVC
PV and PVC need to be deleted first in order to have clean instalation.  
Don't worry, the data won't be deleted if you store it inside /data  
due to data persistent https://minikube.sigs.k8s.io/docs/handbook/persistent_volumes/  
```
kubectl delete pv minio-volume-0
kubectl delete pv minio-volume-1
kubectl apply -f minio-pv.yaml -n minio-dev
kubectl apply -f minio-pvclaim.yaml -n minio-dev
```

## Apply the Secret
minio123 will be chosen as the secret key,  
feel free to change the value in the minio-secret.yaml
```
echo -n 'minio123' | base64
kubectl apply -f minio-secret.yaml -n minio-dev
```

## Deploy Minio
Stateful set, headless service, and service needs to be deployed.  
To know why headless-service is needed, https://stackoverflow.com/questions/52707840/what-is-a-headless-service-what-does-it-do-accomplish-and-what-are-some-legiti
```
kubectl apply -f minio-sts.yaml -n minio-dev
kubectl apply -f minio-headless-service.yaml -n minio-dev
kubectl apply -f minio-service.yaml -n minio-dev
```

if you want to use dns name, execute command below
```
echo "127.0.0.1 minio.kubernetes.net" | sudo tee -a /etc/hosts
```

## Troubleshoot
To troubleshoot, you could type the command below.
```
kubectl get pv
kubectl get pvc -n minio-dev
kubectl get all -n minio-dev
kubectl get pods -n minio-dev
kubectl describe statefulset -n minio-dev
kubectl describe pod datasaku-minio-0 -n minio-dev
kubectl get svc -n minio-dev
```

# Access

To access minio UI reach out to http://minio.kubernetes.net:6543 or 127.0.0.1:6543

# Create Bucket

In the UI, create bucket called **iceberg**
