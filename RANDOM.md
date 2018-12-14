# Alternative: create a NodePort service and access from host

## Figure out the IP and port where you can call the service

```
export SERVICE_HOST=`minikube service --url -n default random-pick-service`

```

## Request service

```
curl -d '["ape","monkey"]' -H "Content-type: application/json" $SERVICE_HOST/random-pick -v
```

