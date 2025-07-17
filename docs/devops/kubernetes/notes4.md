ConfigMap służy do przekazywania konfiguracji, czy zmiennych środowiskowych do podów. Mapy można tworzyć bezpośrednio z plików, np. `app.properties` lub przekazać klucz-wartość. 

```sh
# creates configmap of name: sport, with one key-value-pair: sport-football
kubectl create configmap sport --from-literal=sport-football
```

Mapy można też edytować, a zmiany będą widoczne prawie natychmiast:
```sh
kubectl edit configmap sport 
```

Przykładowo, przy tworzeniu kontenera, możemy użyć ConfigMapy do stworzenia zmiennej środowiskowej:

```sh
...
env:
    - name: FRUITS
        valueFrom:
        configMapKeyRef:
            key: fruits
            name: fruits
...
# creates an environment variable named FRUITS with the value taken from the fruits key in the fruits ConfigMap
```

!!! note info title="Aktualizacje zmiennych środowiskowych"
    Zmiana wartości w ConfigMapie, która jest używana, jako zmienna środowiskowa, nie powoduje zmiany tej zmiennej w już działającym podzie (w przeciwieństwie do zwykłych danych). Można zrestartować pody, wywołując: `kubectl rollout restart deployment/deployment-name`

