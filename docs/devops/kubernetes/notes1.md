```sh
kubectl create deployment hello-minikube --image=kicbase/echo-server:1.0 
kubectl get deployment hello-minikube 

kubectl get pods -l app=hello-minikube
kubectl get pods -A # same as kubectl get pods --all-namespaces

kubectl get logs hello-minikube-ffcbb5874-x6jkt
kubectl describe pod hello-minikube-ffcbb5874-x6jkt
```

```sh
minikube start -p minikube 
minikube profile list

minikube delete --all # deletes all clusters 
```