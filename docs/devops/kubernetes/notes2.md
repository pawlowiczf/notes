## Services

Każdy Pod w Kubernetesie ma unikalny adres IP, ale te adresy nie są widoczne na zewnątrz klastra bez użycia obiektu typu Service. Service pozwala aplikacjom na odbieranie ruchu (np. od użytkownika lub innej aplikacji). Istnieją różne typy usług, które określa się za pomocą pola type w specyfikacji Service:

1. ClusterIP (domyślny) - wystawia usługę pod wewnętrznym adresem IP dostępnym tylko wewnątrz klastra. Tego typu Service jest widoczny wyłącznie dla innych komponentów działających w Kubernetesie. Idealne do komunikacji między Podami.

2. NodePort - wystawia usługę na tym samym porcie na każdym Node w klastrze. Działa poprzez NAT – umożliwia dostęp do usługi z zewnątrz, poprzez adres IP Node i przypisany port. Rozszerza możliwości ClusterIP, ponieważ Service nadal ma wewnętrzny IP, ale dodatkowo jest dostępny z zewnątrz. Używane np. w środowiskach lokalnych lub do szybkiego testowania.

3. LoadBalancer - tworzy zewnętrzny load balancer w infrastrukturze chmurowej (jeśli jest obsługiwany przez danego dostawcę, np. AWS, GCP, Azure). Przydziela publiczny adres IP, przez który można uzyskać dostęp do usługi. Oparty na NodePort i ClusterIP, ale dodaje możliwość automatycznego wystawienia usługi na zewnątrz przez usługę chmurową.

4. ExternalName - nie wystawia usługi przez proxy. Zamiast tego tworzy wpis DNS, który wskazuje na zewnętrzną nazwę domenową (np. foo.bar.example.com). Kubernetes zwraca rekord CNAME, a nie wykonuje żadnego routingu wewnętrznego. Wymaga kube-dns w wersji 1.7 lub nowszej albo CoreDNS 0.0.8 lub nowszej.



```sh
kubectl create deployment kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1

kubectl expose deployment/kubernetes-bootcamp --type="NodePort" --port 8080
minikube service kubernetes-bootcamp 

```

Serwisy grupują zbiór setów na podstawie `label`. 