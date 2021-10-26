# # Kubernetes - Redis task

## requierments
```
minikube
Redis 6.x
A PC / VM with 8GB RAM & 2 CPU
```


## Namespace

```
kubectl create ns redis
```

## Config and Deploy Redis basic configuration

#Create the config and deploy file on the relevant machine
```
cd "redis library"
kubectl apply -n redis -f redis-configmap.yaml
kubectl apply -n redis -f redis-statefulset.yaml
```
#Check pods created:
```
kubectl -n redis get pods
output:
NAME      READY   STATUS    RESTARTS   AGE
redis-0   1/1     Running   0          8m1s
redis-1   1/1     Running   0          7m56s
redis-2   1/1     Running   0          7m51s
```
#Check logs of the pods:
```
kubectl -n redis logs redis-"number"
```

## Test replication status

```
kubectl -n redis exec -it redis-0 sh
redis-cli 
auth "masterauth"
info replication
```
## Set redis to debug mode
In redis configmap set:
```
loglevel debug

stslog-enabled yes
```

## Enable TLS secure
Need to create both redis.key & redis.crt
```
$openssl genrsa -out redis.key 4098
$req -new -key redis.key -out server.csr

fill in your connection detailes

$openssl x509 -req -days 366 -in server.csr  -signkey redis.key -out server.crt

$kubectl apply -n redis -f redis-configmap-TLSSECURED.yaml


```

verify TLS connection

```
$kubectl -n redis exec -it redis-0 sh

redis-cli --tls --cert ./redis.crt ./redis.key ./cacert ./ca.crt

127.0.0.1:6379>ping
pong 
if you recived a pong massage - you secured connection succseeded 
```
