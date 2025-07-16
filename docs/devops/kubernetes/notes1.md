```sh
kubectl create deployment hello-minikube --image=kicbase/echo-server:1.0 
kubectl get deployment hello-minikube 

kubectl get pods -l app=hello-minikube
kubectl get pods -A # same as kubectl get pods --all-namespaces
kubectl get nodes 

kubectl get logs hello-minikube-ffcbb5874-x6jkt
kubectl describe pod hello-minikube-ffcbb5874-x6jkt
```

```sh
# get current context's info
kubectl config current-context

# list all saved contexts 
kubectl config get-contexts 

kubectl delete deployments/kubernetes-bootcamp
kubectl delete services/kubernetes-bootcamp
```


```sh
minikube start -p minikube 
minikube profile list

minikube delete --all # deletes all clusters 

minikube ip
```
