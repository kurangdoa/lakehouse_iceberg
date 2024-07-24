# Instalation

based on https://docs.dagster.io/deployment/guides/kubernetes/deploying-with-helm

## Create Namespace
```
kubectl delete namespace dagster-dev
kubectl create namespace dagster-dev
```


## Create Secret to Minio
```
kubectl create secret -n dagster-dev generic dagster-aws-access-key-id --from-literal=AWS_ACCESS_KEY_ID=minio
kubectl create secret -n dagster-dev generic dagster-aws-secret-access-key --from-literal=AWS_SECRET_ACCESS_KEY=minio123
```

## Add Helm Repo
```
helm repo add dagster https://dagster-io.github.io/helm
```

## Install Helm Chart
```
helm upgrade --install dagster dagster/dagster -f config.yaml --namespace dagster-dev --debug
```

## Test User Deployment
```
eval $(minikube -p datasaku-cluster docker-env)
docker build -f ./test_python_image/Dockerfile -t test-python-image:1.0.0 ./test_python_image
```

## Troubleshoot
```
kubectl get all -n dagster-dev
kubectl get jobs -n dagster-dev
```