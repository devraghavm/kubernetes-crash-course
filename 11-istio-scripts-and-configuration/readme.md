# Setting up istio

```
gcloud container clusters get-credentials standard-cluster-1 --zone=europe-west1-b
kubectl create namespace istio-system
curl -L https://git.io/getLatestIstio | ISTIO_VERSION=1.2.2 sh -
cd istio-1.2.2
for i in install/kubernetes/helm/istio-init/files/crd*yaml; do kubectl apply -f $i; done
helm template install/kubernetes/helm/istio --name istio --set global.mtls.enabled=false --set tracing.enabled=true --set kiali.enabled=true --set grafana.enabled=true --namespace istio-system > istio.yaml
kubectl apply -f istio.yaml

kubectl label namespace default istio-injection=enabled
```

Latest Istio: ([Istio](https://istio.io/v1.6/docs/setup/getting-started/))

Followed below steps instead:
```
gcloud container clusters get-credentials standard-cluster-1 --zone=europe-west1-b
kubectl create namespace istio-system
curl -L https://istio.io/downloadIstio | sh -
cd istio-1.6.8
export PATH=$PWD/bin:$PATH
istioctl install --set profile=demo

kubectl label namespace default istio-injection=enabled

```

Windows - https://curl.haxx.se/windows/

# Using Kiali

- http://localhost:20001

```
kubectl port-forward \
    $(kubectl get pod -n istio-system -l app=kiali \
    -o jsonpath='{.items[0].metadata.name}') \
    -n istio-system 20001
```
Create a secret
```
kubectl get secret -n istio-system kiali
kubectl create secret generic kiali -n istio-system --from-literal=username=admin --from-literal=passphrase=admin
```

# Using Graphana to see prometheus metrics
- http://localhost:3000

```
kubectl -n istio-system port-forward \
    $(kubectl -n istio-system get pod -l app=grafana \
    -o jsonpath='{.items[0].metadata.name}') 3000
```

# Using Jaeger

http://localhost:16686

```
kubectl port-forward -n istio-system \
    $(kubectl get pod -n istio-system -l app=jaeger \
    -o jsonpath='{.items[0].metadata.name}') 16686
```