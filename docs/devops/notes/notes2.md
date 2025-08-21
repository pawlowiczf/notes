Podstawowe informacje o [Docker compose](../docker/notes2.md)

Mamy kilka serwisów i chcemy mieć możliwość ich uruchomienia, ale też pracy nad konkretnym. 

W każdym serwisie umieszczamy, w głównym folderze, plik `docker-compose.yaml`, który konfiguruje uruchomienie każdego serwisu (wraz z jego bazą danych) i plik `Dockerfile` do budowania obrazu aplikacji. 

Zmienne środowiskowe do uruchomienia aplikacji i bazy danych pobierane są z odpowiedniego folderu danego serwisu (np. `../product-service/docker/`) - więc w każdym serwisie tworzymy też katalog `/docker` zawierający np. `app-env.txt`, `database-env.txt` i `database-password.txt`. 

W tym folderze `/docker` umieszczamy też osobny plik `docker-compose.yaml`, który uruchamia lokalną bazę danych, do pracy w izolacji od innych. 

```bash
service/
├── Dockerfile
├── .env
├── docker-compose.yaml
└── docker/
    ├── docker-compose.yaml
    ├── app-env.txt
    ├── database-password.txt
    └── database-env.txt
    
```

Dla każdego serwisu, główny plik `docker-compose.yaml` jest taki sam - korzystamy natomiast z profili i możliwości definiowania serwisów, które będą uruchamiane (robi się to w pliku `.env`). 

```bash
...
COMPOSE_PROFILES=payment-service,order-service,cart-service,kafka
...
```

Lista plików: 

- [Main docker-compose.yaml](docker-structure/docker-compose-main.yaml)
- [Local docker-compose.yaml](docker-structure/docker-compose-local.yml)
- [Dockerfile](docker-structure/Dockerfile)
- [.env](docker-structure/env.txt)
- [database-env.txt](docker-structure/database-env.txt)
- [database-password.txt](docker-structure/database-password.txt)
- [app-env.txt](docker-structure/app-env.txt)

```properties title="application.properties"
spring.application.name=Product service
spring.profiles.active=dev

spring.jpa.hibernate.ddl-auto=none
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.PostgreSQLDialect
spring.jpa.show-sql=true
```

```properties title="application-dev.properties"
spring.datasource.url=jdbc:postgresql://localhost:5432/product-service-db
spring.datasource.username=admin
spring.datasource.password=admin

spring.kafka.bootstrap-servers=localhost:9094

spring.kafka.consumer.group-id=product-service
spring.kafka.consumer.auto-offset-reset=earliest
spring.kafka.consumer.key-deserializer=org.apache.kafka.common.serialization.StringDeserializer
spring.kafka.consumer.value-deserializer=org.apache.kafka.common.serialization.StringDeserializer

spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer
spring.kafka.producer.value-serializer=org.apache.kafka.common.serialization.StringSerializer
```

```properties title="application-compose.properties"
spring.datasource.url=jdbc:postgresql://product-service-db:5432/product-service-db

spring.kafka.bootstrap-servers=kafka:9092

spring.kafka.consumer.group-id=product-service
spring.kafka.consumer.auto-offset-reset=earliest
spring.kafka.consumer.key-deserializer=org.apache.kafka.common.serialization.StringDeserializer
spring.kafka.consumer.value-deserializer=org.apache.kafka.common.serialization.StringDeserializer

spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer
spring.kafka.producer.value-serializer=org.apache.kafka.common.serialization.StringSerializer
```