# opa-JWT-validation
OPA (OPA-Istio) that allows you to enforce OPA policies at the Istio Proxy layer. This is a example of how to validate a JWT at OPA level means validate the JWT before reaching to the service because OPA work as HTTP filter to enovy proxy in the istio service mesh setup. In the JWT validation we can also validate some static attributes without decoding it. OPA gives good support to validate the JWT for more info you can refer [OPA documentation](https://openpolicyagent.org/docs/latest/). 

### Table of contents
##### Getting started
##### Running
##### Example

### Getting Started
Requirements - To run this example you will need minikube, kubectl, Docker, istio.


### Running
Steps to run this templete-

1> Start minikube
```sh
minikube start --vm-driver=none
```
2> Install istio on minikube cluster
```sh
istioctl manifest apply --set profile=demo
```
3> Ensure all pods are running
```sh
kubectl get pods -n istio-system
```
4> Now apply quick_start.yaml
```sh
kubectl apply -f quick_start.yaml
```
5> Enable the opa and istio injection for service pods
```sh
kubectl label namespace default opa-istio-injection="enabled"

kubectl label namespace default istio-injection="enabled"
```
6> Now apply bookinfo.yaml
```sh
kubectl apply -f bookinfo.yaml
```
7> Create a gateway for your service mesh by applying bookinfo-gateway.yaml
```sh
kubectl apply -f bookinfo-gateway.yaml
```
8> Node Port
```sh
export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].nodePort}')

export INGRESS_HOST=$(minikube ip)

export GATEWAY_URL=$INGRESS_HOST:$INGRESS_PORT 

echo $GATEWAY_URL
```
Now check the authorization by following curl commands

### Example
- 
```sh
curl -X GET \
  'http://$GATEWAY_URL/productpage' \
  -H 'Postman-Token: 530bdf79-b496-4cac-bff8-7c08484a90a9' \
  -H 'authorization: eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJwZXRlciIsImF1ZCI6IiouZWNvbW0uY29tIiwibmJmIjoxNTczMTA5MDA3LCJ1c2VyX25hbWUiOiJwZXRlciIsInNjb3BlIjpbImZvbyJdLCJpc3MiOiJzdHMuZWNvbW0uY29tIiwiZXhwIjoxNTczMTE1MDA3LCJpYXQiOjE1NzMxMDkwMDcsImF1dGhvcml0aWVzIjpbIlJPTEVfVVNFUiJdLCJqdGkiOiIwNjJjNTliYy05MGE2LTQ5MjMtYjY3OS1kZTdhYWVjYzIwMGIiLCJjbGllbnRfaWQiOiJhcHBsaWNhdGlvbmlkIn0.pp6aCSx5fcZQXBgOHHGFZS8lkJtAS2F-Hm2pKc37yj3IbpMBenB_XCUepLn9B5HiF_AOM0CGxor5x4YmBo6EWkYMNing7i-NDVDMmzCSBTBV2YkH5xkqAseGMeNl46LHJybiamN4L_q3xx6XQYsuvV3YvV5YLjnp5wfwM2UPv1xRYIm4u3u2_3Z7oHJ7f6oWGfls_sPdYnjYCirb5Geog6Po-X-RJFr1gXVke1N2RnG9B5d2nt_DLKx5vVHDJTkbxrM2qlsQcu6yqqzlEVTvQgXyz4-HtnwckN3fL0N6b3mZEqggnJSZpS_b-tPS20ZBnvqsWVy5UR4JjwCF-L5lWA' \
  -H 'cache-control: no-cache'
```
- 
```sh
curl -X GET \
  'http://$GATEWAY_URL/api/v1/products' \
  -H 'Postman-Token: 530bdf79-b496-4cac-bff8-7c08484a90a9' \
  -H 'authorization: eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJwZXRlciIsImF1ZCI6IiouZWNvbW0uY29tIiwibmJmIjoxNTczMTA5MDA3LCJ1c2VyX25hbWUiOiJwZXRlciIsInNjb3BlIjpbImZvbyJdLCJpc3MiOiJzdHMuZWNvbW0uY29tIiwiZXhwIjoxNTczMTE1MDA3LCJpYXQiOjE1NzMxMDkwMDcsImF1dGhvcml0aWVzIjpbIlJPTEVfVVNFUiJdLCJqdGkiOiIwNjJjNTliYy05MGE2LTQ5MjMtYjY3OS1kZTdhYWVjYzIwMGIiLCJjbGllbnRfaWQiOiJhcHBsaWNhdGlvbmlkIn0.pp6aCSx5fcZQXBgOHHGFZS8lkJtAS2F-Hm2pKc37yj3IbpMBenB_XCUepLn9B5HiF_AOM0CGxor5x4YmBo6EWkYMNing7i-NDVDMmzCSBTBV2YkH5xkqAseGMeNl46LHJybiamN4L_q3xx6XQYsuvV3YvV5YLjnp5wfwM2UPv1xRYIm4u3u2_3Z7oHJ7f6oWGfls_sPdYnjYCirb5Geog6Po-X-RJFr1gXVke1N2RnG9B5d2nt_DLKx5vVHDJTkbxrM2qlsQcu6yqqzlEVTvQgXyz4-HtnwckN3fL0N6b3mZEqggnJSZpS_b-tPS20ZBnvqsWVy5UR4JjwCF-L5lWA' \
  -H 'cache-control: no-cache'
  ```
  In both CURL we are using valid JWT so you will get 200 OK response if you remove some characters from JWT then you will get 403 Foridden because in the rego policy we are validating the JWT also with the decoding.
