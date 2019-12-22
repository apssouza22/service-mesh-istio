# Service mesh example

This repository tries to provide a bootstrap example of a service mesh using Istio


### Enabled services:
#### Features
- Load balance
- Circuit beak
- TLS connection
- Service discovery
- Traffic management 
 
#### Telemetry 
- Grafana
- Kiali
- Jaeger 


# How to use
* `minikube start --memory 4096` If you have more memory I sugest increase the memory
* `kubectl create ns istio-system`
* `kubectl apply -f 1-istio-init.yaml`
* `kubectl apply -f 2-istio-minikube-reduced.yaml`
* `kubectl apply -f 3-kiali-secret.yaml`
* `kubectl apply -f 4-label-default-namespace.yaml`
* Wait until all pods are up and running, you can watch it with `kubectl get po -n istio-system -w`
* `kubectl apply -f 5-application-no-istio.yaml`
* `kubectl apply -f 6-istio-gateway-rules.yaml`

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

## Circuit break test
Running the command below you will see 3 request failed 
then the circuit will  open to the sick pod and no errors will appear anymore

`while true; do curl $GATEWAY/api/vehicles/driver/City%20Truck; echo; done;`

### Cleanup

* Delete the cluster
``minikube delete``

