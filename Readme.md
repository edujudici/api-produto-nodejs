# API NodeJS utilizando KIND

- Criação de um cluster kubernetes com kind no ambiente local

## Config yaml

- Configuração para criar multi nodes com bind de portas

```
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  extraPortMappings:
  - containerPort: 30001
    hostPort: 8080
    listenAddress: "0.0.0.0"
    protocol: TCP  
- role: worker
- role: worker
```

## Executando o yaml para criação do Cluster

```console
kind create cluster --name multinode --config=config.yaml
```
## Containers em execução: docker container ls

```
CONTAINER ID        IMAGE                  COMMAND                  CREATED             STATUS              PORTS                                                NAMES
77185ebe55d2        kindest/node:v1.19.1   "/usr/local/bin/entr…"   4 minutes ago       Up 2 minutes        127.0.0.1:35373->6443/tcp, 0.0.0.0:8080->30001/tcp   multinode-control-plane
6a81f5ea0deb        kindest/node:v1.19.1   "/usr/local/bin/entr…"   4 minutes ago       Up 2 minutes                                                             multinode-worker2
91f1cb9f3d78        kindest/node:v1.19.1   "/usr/local/bin/entr…"   4 minutes ago       Up 2 minutes                                                             multinode-worker
```

- Observe o bind da porta 8080 com a 30001 do container.

# Deployment

## Realizando o deploy do serviço

```console
kubectl apply -f ./ -R
```
```
➜  kind kubectl apply -f ./ -R
deployment.apps/api-deployment created
service/api-service created
deployment.apps/mongodb-deployment created
service/mongo-service created
```
- kubectl get all
```
➜  kind kubectl get all
NAME                                      READY   STATUS    RESTARTS   AGE
pod/api-deployment-845798dff5-5d59b       1/1     Running   2          82m
pod/mongodb-deployment-6b7bd66f46-g2nbz   1/1     Running   1          82m

NAME                    TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/api-service     LoadBalancer   10.96.228.90    <pending>     80:30001/TCP   82m
service/kubernetes      ClusterIP      10.96.0.1       <none>        443/TCP        97m
service/mongo-service   ClusterIP      10.96.121.208   <none>        27017/TCP      82m

NAME                                 READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/api-deployment       1/1     1            1           82m
deployment.apps/mongodb-deployment   1/1     1            1           82m

NAME                                            DESIRED   CURRENT   READY   AGE
replicaset.apps/api-deployment-845798dff5       1         1         1       82m
replicaset.apps/mongodb-deployment-6b7bd66f46   1         1         1       82m
```

## Acessando a aplicação
```
localhost:8080/api-docs
```

## Referências

- https://kind.sigs.k8s.io/docs/user/quick-start/
- https://kind.sigs.k8s.io/docs/user/configuration/