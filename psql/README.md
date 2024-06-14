# Installation

## Postgresql

The instalation will be based on psql-ha by bitnami [link](https://artifacthub.io/packages/helm/bitnami/postgresql-ha) to provide better availability.

password to be used by Kubernetes will be encoded by using this command below.
```
echo -n 'postgres' | base64
```

Make sure the namespace is created 
```
kubectl delete namespace psql-dev
kubectl create namespace psql-dev
```

Apply secret in kubernetes to be used inside helm chart
```
kubectl apply -n psql-dev -f psql-secret.yaml
```

Create volume and volume claim
```
kubectl delete pv postgres-volume-0
kubectl delete pv postgres-volume-1
kubectl apply -n psql-dev -f psql-pv.yaml
kubectl get pv
kubectl apply -n psql-dev -f psql-pvclaim.yaml 
kubectl get pvc -n psql-dev
```

Helm upgrade and see the status of deployment
```
helm upgrade --cleanup-on-fail \
  --install datasaku-postgres oci://registry-1.docker.io/bitnamicharts/postgresql-ha \
  --namespace psql-dev \
  --create-namespace \
  --version=14.1.2 \
  --values config.yaml --debug
kubectl get all -n psql-dev
kubectl get pvc -n psql-dev
kubectl describe pvc data-datasaku-postgres-postgresql-ha-postgresql-1 -n psql-dev
kubectl describe pod/datasaku-postgres-postgresql-ha-postgresql-0 -n psql-dev
```

## PGAdmin

Password for PGAdmin with username admin@admin.com
```
echo -n 'admin123' | base64
```

Deploy PGAdmin
```
kubectl apply -n psql-dev  -f pgadmin-secret.yaml
kubectl apply -n psql-dev  -f pgadmin-deployment.yaml
kubectl apply -n psql-dev  -f pgadmin-service.yaml
kubectl get svc -n psql-dev
```

# Access

To access PGAdmin UI, you could reach it through 127.0.0.1:5432

# (optional) instalation for Nessie

create database called *nessie* in pgadmin

by connecting first 
hostname: datasaku-postgres-postgresql-ha-pgpool.psql-dev.svc.cluster.local
port: 5433