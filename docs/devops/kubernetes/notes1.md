```sh
kubectl create deployment hello-minikube --image=kicbase/echo-server:1.0
kubectl get deployment hello-minikube

kubectl get pods -l app=hello-minikube # get pods with such a key-value label
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

# add label version=v1 to the pod
kubectl label pods kubernetes-bootcamp-12c24d version=v1
```


```sh
minikube start -p minikube
minikube profile list

minikube delete --all # deletes all clusters

minikube ip
```

```sh title="AWS"
aws sts get caller identity
aws eks update-kubeconfig --region eu-central-1 --name staging-demo
kubectl get nodes
kubectl auth can-i "*" "*"

aws configure --profile developer
kubectl config view --minify

kubectl get pods -n kube-system
kubectl top pods -n kub-system

kubectl get hpa -n 3-example
kubectl port-forward svc/myapp 8080 -n 3-example
```