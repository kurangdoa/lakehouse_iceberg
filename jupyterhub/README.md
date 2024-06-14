# Instalation
based on https://z2jh.jupyter.org/en/stable/kubernetes
based on https://github.com/jupyter/docker-stacks

## Mount Folder to Pod
```
minikube mount /Users/rhyando/code/development/jupyterhub/data:/data/jupyter -p datasaku-cluster
```

## Docker Build
```
eval $(minikube -p datasaku-cluster docker-env)
docker build --rm --force-rm \
    -t pyspark-notebook-custom ./pyspark-notebook-docker-stacks \
    --build-arg openjdk_version=11 \
    --build-arg spark_version=3.5.1 \
    --build-arg hadoop_version=3 \
    --build-arg spark_download_url="https://archive.apache.org/dist/spark/"
```

## (Optional) Check Spark Version
```
docker run -it --rm pyspark-notebook-custom pyspark --version
```

## Put the Docker Image into Registry
based on https://www.docker.com/blog/how-to-use-your-own-registry-2/
```
docker run -d -p 5555:5000 --name registry registry:2
docker tag pyspark-notebook-custom:latest localhost:5555/pyspark-notebook-custom:latest
docker push localhost:5555/pyspark-notebook-custom:latest
```

## Helm Update
```
helm repo add jupyterhub https://hub.jupyter.org/helm-chart/
helm repo update
```

## Create Namespace
```
kubectl delete namespace jupyter-dev
kubectl create namespace jupyter-dev
```

## Create PV and PVC
```
kubectl delete pv jupyter-volume
kubectl apply -n jupyter-dev -f jupyter-pv.yaml
kubectl apply -n jupyter-dev -f jupyter-pvclaim.yaml
```

## Deployment
```
helm upgrade --cleanup-on-fail \
  --install datasaku-jupyterhub jupyterhub/jupyterhub \
  --namespace jupyter-dev \
  --create-namespace \
  --version=3.3.7 \
  --values config.yaml
```

## Troubleshoot
```
kubectl get pv -n jupyter-dev
kubectl get pvc -n jupyter-dev
kubectl get pod -n jupyter-dev
kubectl get svc -n jupyter-dev
```