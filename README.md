# Service mesh example

This repository tries to provide a bootstrap example of a service mesh using Istio

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
* `minikube start --memory 4096` If you have more memory I sugest increase the memory
* `kubectl apply -f ./istio/`
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

### Cleanup

* Delete the cluster
``kubectl delete -f ./``
``kubectl delete -f ./istio``

