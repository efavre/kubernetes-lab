# Prerequisite

Node
Docker
Kubernetes

# Chapter 0: run app

## Run locally:

```
cd random-pick
npm install
npm test
npm start
```

## Request service

```
curl -d '["ape","monkey"]' -H "Content-type: application/json" http://localhost:3000/random-pick -v
```

# Chapter 1: Run docker app

## Build image and run container

```
cd random-pick
docker build -t random-pick .
docker run -p 3000:3000 --name random-pick -d random-pick
```

## Request service

```
curl -d '["ape","monkey"]' -H "Content-type: application/json" http://localhost:3000/random-pick -v
```

## Stop container

```
docker stop random-pick
```
This docker image is actually deployed on [docker hub](https://hub.docker.com/r/efavre/random-pick/). The next step will pull the image from there.

# Chapter 2: run from kubnernetes


## Start minikube

Minikube is a tool that allows to run a single node kubernetes cluster on a local machine for test / sandbox purpose.
```
minikube start
```

## Create deployment to run application

We can now create the deployment resource which will deploy a pod with the random-pick application running.
```
kubectl create -f kubernetes/random-pick-deployment.yaml
kubectl describe deployment random-pick-deployment
```

## Create service to expose application inside cluster

This service will make it possible for the random-pick pod to be requested from other pods on the cluster.
```
kubectl create -f kubernetes/random-pick-cluster-service.yaml
kubectl describe service random-pick-service
```

## Request service

To test that the service and the application are running properly, we can expose the service to the host through port forwarding
```
kubectl port-forward services/random-pick-service 3000:3000
```

From another terminal:
```
curl -d '["ape","monkey"]' -H "Content-type: application/json" http://localhost:3000/random-pick -v
```

Alternatively, you can access the service through the kubectl proxy. Start the proxy:

```
kubectl proxy --port=8080
```

Then request the proxyed endpoint:
```
curl -d '["ape","monkey"]' -H "Content-type: application/json" http://localhost:8080/api/v1/namespaces/default/services/random-pick-service:http/proxy/random-pick -v
```

# Chapter 3: expose service to the world

We now want our service to be accessible from the internet. To do that we need to create an ingress.

## Create ingress

```
kubectl apply -f kubernetes/ingress.yaml
```

## Request service

```
curl -d '["ape","monkey"]' -H "Content-type: application/json" http://$(minikube ip)/random-pick -v
```

# Chapter 4: integration
Let's create a new application, animal-pick, which calls the random-pick service providing a persisted list of animals as a request body.

## Create docker image

```
cd animal-pick
docker build -t animal-pick .
docker run -p 3001:3001 --name animal-pick -d animal-pick
```

## Create deployment and service

```
kubectl apply -f kubernetes/animal-pick-deployment.yaml 
kubectl apply -f kubernetes/animal-pick-cluster-service.yaml 
```

## Request service

To test that the service and the application are running properly, and that they are properly integrated with the random-pick application, we can expose the service to the host through port forwarding:
```
kubectl port-forward services/animal-pick-service 3001:3001
```

From another terminal:
```
curl http://localhost:3001/animal-pick
```

# Chapter 6: database

# Chapter 5: Secrets

