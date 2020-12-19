# Service mesh example

This repository tries to provide a bootstrap example of a service mesh using Istio

We have recorded some screencast presenting this project. [Check it out!](https://dzone.com/articles/programming-microservices-communication-with-istio-state)

![Alt text](mesh-arch.png?raw=true "microservices architecture")

### Enabled services:
#### Features
- Load balancing
- Circuit breaker
- TLS connection
- Service discovery
- Traffic management 
- External call monitoring
- GRPC load balancing
- Custom logging 
- Authentication
- Fault injection
- Traceability 
- Observability 

#### Telemetry 
- Grafana
- Kiali
- Jaeger 


# How to use
* Install Minikube  https://kubernetes.io/docs/tasks/tools/install-minikube/
* `minikube start --memory 4096` If you have more memory I suggest increasing the memory
* `kubectl apply -f ./istio/` Some components depends on the Istio init, so I recommend you keep running this command until all warnings go away
* Wait until all pods are up and running, you can watch it with `kubectl get po -n istio-system -w`
* `kubectl apply -f ./`


Getting the url to access the system
```
export INGRESS_HOST=$(minikube ip)
export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].nodePort}')
export GATEWAY=$INGRESS_HOST:$INGRESS_PORT
echo $GATEWAY
```

Visit the address printed on the Browser
 
## Monitoring

* Visit $INGRESS_HOST:31000 on the browser to access Kiali. credential admin - admin
* Visit $INGRESS_HOST:31001 on the browser to access Jaeger
* Visit $INGRESS_HOST:31002 on the browser to access Grafana

## Checking Circuit break
Running the command below you will see 3 request failed 
then the circuit will  open to the sick pod and no errors will appear anymore

`while true; do curl $GATEWAY/api/vehicles/driver/City%20Truck; echo; done;`

## Checking Load balancing
Running the command below you will see the logs from the call result from different hostname:

`kubectl logs -l app=greeter-client -c greeter-client -f`

## Checking custom logging 
Running the command below you will see the logs printed:

``kubectl logs -n istio-system -l istio-mixer-type=telemetry -c mixer | grep "newlog" ``


## Authorization 
When enabled the JWT Authentication we need to set the JWT token to our request header

Add to your request the Authorization with the token below
 
``Bearer eyJhbGciOiJSUzI1NiIsImtpZCI6IkRIRmJwb0lVcXJZOHQyenBBMnFYZkNtcjVWTzVaRXI0UnpIVV8tZW52dlEiLCJ0eXAiOiJKV1QifQ.eyJleHAiOjQ2ODU5ODk3MDAsImZvbyI6ImJhciIsImlhdCI6MTUzMjM4OTcwMCwiaXNzIjoidGVzdGluZ0BzZWN1cmUuaXN0aW8uaW8iLCJzdWIiOiJ0ZXN0aW5nQHNlY3VyZS5pc3Rpby5pbyJ9.CfNnxWP2tcnR9q0vxyxweaF3ovQYHYZl82hAUsn21bwQd9zP7c-LS9qd_vpdLG4Tn1A15NxfCjp5f7QNBUo-KC9PJqYpgGbaXhaGx7bEdFWjcwv3nZzvc7M__ZpaCERdwU7igUmJqYGBYQ51vr2njU9ZimyKkfDe3axcyiBZde7G6dabliUosJvvKOPcKIWPccCgefSj_GNfwIip3-SsFdlR7BtbVUcqR-yv-XOxJ3Uc1MI0tz3uMiiZcyPV7sNCU4KRnemRIMHVOfuvHsU60_GhGbiSFzgPTAa9WTltbnarTbxudb_YEOx12JiwYToeX0DCPb43W1tzIBxgm8NxUg``

### Cleanup

* Delete the cluster
``kubectl delete -f ./``
``kubectl delete -f ./istio``

## That's all. Leave a star if it helped you!
 
