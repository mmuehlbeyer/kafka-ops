# Strimzi Kafka Minikube

## Kafka with single replica
#### start minikube

```bash
minikube start --memory=4096
```
# create namespace
```bash
kubectl create namespace kafka
```

# apply install files
```bash
kubectl create -f 'https://strimzi.io/install/latest?namespace=kafka' -n kafka
```