# instalation
based on https://projectnessie.org/nessie-latest/configuration/#swagger-ui

## Create Namespace
Create the namespace
```
kubectl delete namespace nessie-dev
kubectl create namespace nessie-dev
```

## Apply Secret for PSQL
Apply the secret
```
kubectl apply -f nessie-secret.yaml -n nessie-dev
```

## Create PV and PVC
Create PV and PVC
```
kubectl delete pv nessie-volume -n nessie-dev --grace-period=0 --force
kubectl delete pvc nessie-volume-claim -n nessie-dev
kubectl apply -n nessie-dev -f nessie-pv.yaml
kubectl apply -n nessie-dev -f nessie-pvclaim.yaml
```

## Deploy Nessie
Deploy Nessie
```
kubectl apply -n nessie-dev -f nessie-headless.yaml
kubectl apply -n nessie-dev -f nessie-deployment.yaml
kubectl apply -n nessie-dev -f nessie-service.yaml
```

## Troubleshoot
Troubleshoot the deployment
```
kubectl get pv -n nessie-dev
kubectl get pvc -n nessie-dev
kubectl get deployments -n nessie-dev
kubectl get pods -n nessie-dev -o wide
kubectl get svc -n nessie-dev
kubectl get all -n nessie-dev
```

# Access
To access nessie UI, reach out to 127.0.0.1:6788