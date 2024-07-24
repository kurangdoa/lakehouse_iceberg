# minio
based on https://github.com/minio/minio/issues/6775

## Create a Namespace
```
kubectl delete namespace minio-dev
kubectl create namespace minio-dev
```

## Create PV and PVC
```
kubectl delete pv minio-volume-0
kubectl delete pv minio-volume-1
kubectl apply -f minio-pv.yaml -n minio-dev
kubectl apply -f minio-pvclaim.yaml -n minio-dev
```

## Apply the Secret
```
echo -n 'minio123' | base64
kubectl apply -f minio-secret.yaml -n minio-dev
```

## Deploy Minio
```
kubectl apply -f minio-sts.yaml -n minio-dev
kubectl apply -f minio-headless-service.yaml -n minio-dev
kubectl apply -f minio-service.yaml -n minio-dev
kubectl apply -f minio-ingress.yaml -n minio-dev
```

if you want to use dns name, execute command below
```
echo "127.0.0.1 minio.kubernetes.net" | sudo tee -a /etc/hosts
```

## Troubleshoot
kubectl get pv
kubectl get pvc -n minio-dev
kubectl get all -n minio-dev
kubectl get pods -n minio-dev
kubectl describe statefulset -n minio-dev
kubectl describe pod datasaku-minio-0 -n minio-dev
kubectl get svc -n minio-dev

# Access

To access minio UI reach out to http://minio.kubernetes.net:6543 or 127.0.0.1:6543

# Create Bucket

In the UI, create bucket called **iceberg**


# Optional

## create nfs

echo "$(realpath .)/_data -alldirs -mapall="$(id -u)":"$(id -g)" $(minikube ip -p datasaku-cluster)"  | sudo tee -a /etc/exports && sudo nfsd update

sudo vi /etc/exports
echo "/Users/rhyando/code/development/_data -network 192.168.49.0 -mask 255.255.255.0" | sudo tee -a /etc/exports && sudo nfsd update

showmount -e

echo "/Users/rhyando/code/development/_data -network 192.168.0.0 -mask 255.255.0.0 " | sudo tee -a /etc/exports && sudo nfsd update

sudo mount 192.168.49.1:/Users/rhyando/code/development/_data /data

sudo mount -t nfs -o nfsvers=3 192.168.178.241:/Users/rhyando/code/development/_data /var/data