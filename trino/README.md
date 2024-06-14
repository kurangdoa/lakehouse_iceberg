# Instalation

based on helm chart trnio

## Deployment
```
kubectl delete namespace trino-dev
helm repo add trino https://trinodb.github.io/charts
helm upgrade --cleanup-on-fail \
  --install datasaku-trino trino/trino \
  --namespace trino-dev \
  --create-namespace \
  --values catalog.yaml
  kubectl apply -n trino-dev -f trino-service.yaml
```

## Troubleshoot
```
kubectl get all -n trino-dev
```
# Access 

To access trino CLI, follow this command below.
With prerequisite of 
```
./trino http://127.0.0.1:8765
```