
Kubernetes umożliwia wprowadzanie zmian bez przerwy w działaniu aplikacji. Gdy pojawia się nowa wersja, tworzy nowego poda i usuwa starego - jest to zatem taki proces iteracyjny, aż zostaną same nowe pody. 

```sh title="Aktualizacja obrazu to nowszej wersji"
# 1. update image to a newer version 
kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=docker.io/jocatalin/kubernetes-bootcamp:v2

# 2. check status of the rollout
kubectl rollout status deployments/kubernetes-bootcamp

# 3. check if pods are running correctly
kubectl get pods

# 4. make a mistake - image doesn't exist in repository
kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=gcr.io/google-samples/kubernetes-bootcamp:v10

# 5. revert the deployment to the previous version 
kubectl rollout undo deployments/kubernetes-bootcamp
```

```sh
kubectl rollout history deployment/myapp
kubectl rollout undo deployment/myapp --to-revision=2
```