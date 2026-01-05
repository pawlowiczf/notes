Podstawowe informacje o [Docker compose](./notes2.md)

W pracy nad mikroserwisami, potrzeba jest uruchomienia wielu serwisów jednocześnie oraz automatycznego, ponownego budowania obrazu w przypadku dokonania zmian w kodzie źródłowym. Najlepiej stworzyć jedno rezpozytorium do zarządzania konfiguracją i uruchomieniem systemu - w ten sposób zachowujemy `source of truth`. Ogólnie, plik `Dockerfile` powinien być zlokalizowany osobno, w każdym serwisie, zatem do stawiania systemu, wszystkie potrzebne serwisy powinny być dostępne. Proponuję poniższą strukturę katalogów:

```
configuration-service/
├── .env
├── compose.yaml
└── env/
    ├── product-service/
        ├── app-env.txt
        ├── database-password.txt
        ├── database-env.txt

```

W pliku `.env` umieszcza się zmienne środowiskowe, które będą używane i interpolowane w pliku `compose.yml`.
```bash
...
COMPOSE_PROFILES=payment-service,order-service,cart-service,kafka
ESCAPE_PATH=../..

PRODUCT_SERVICE_PORT=8400
PRODUCT_SERVICE_DB_PORT=9400
PRODUCT_SERVICE_GRPC_PORT=7400
...

```yaml
product-service:
    build:
        context: ${ESCAPE_PATH}/product-service
        dockerfile: Dockerfile
    # container_name: product-service
    volumes:
        - ${HOME_PATH}/.m2:/root/.m2
    ports:
        - "${PRODUCT_SERVICE_PORT:?error}:8080"
        - "${PRODUCT_SERVICE_GRPC_PORT:?error}:50051"
    env_file:
        -
            path: ./env/product-service/app-env.txt
            required: false
    environment:
        - SPRING_PROFILES_ACTIVE=${PRODUCT_SERVICE_SPRING_PROFILES_ACTIVE:-compose}
    depends_on:
        product-service-db:
            restart: true
            condition: service_healthy
        kafka:
            condition: service_healthy
    profiles: [product-service]
#
product-service-db:
    image: postgres:15-alpine
    # container_name: product-service-db
    healthcheck:
        test: ["CMD-SHELL", "pg_isready -U \"$$POSTGRES_USER\" -d \"$$POSTGRES_DB\""]
        interval: 10s
        retries: 5
        start_period: 30s
        timeout: 10s
    ports:
        - "${PRODUCT_SERVICE_DB_PORT:?error}:5432"
    volumes:
        - ${ESCAPE_PATH}/product-service/docker/init.sql:/docker-entrypoint-initdb.d/init.sql
        - product-service-db:/var/lib/postgresql/data
    secrets:
        - product-service-db-password
    environment:
        - POSTGRES_USER=admin
        - POSTGRES_DB=product-service-db
        - POSTGRES_PASSWORD_FILE=/run/secrets/product-service-db-password
    env_file:
        -
            path: ./env/product-service/database-env.txt
            required: false
    profiles: [product-service]
#
```

```Dockerfile
FROM maven:3.9-eclipse-temurin-21 AS builder
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn clean package -DskipTests -DfailOnNoContracts=false

FROM eclipse-temurin:21-jdk AS runtime
WORKDIR /app

COPY --from=builder /app/target/*.jar ./app.jar
CMD ["java", "-jar", "app.jar"]
```

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

```

```properties title="application-compose.properties"
spring.datasource.url=jdbc:postgresql://product-service-db:5432/product-service-db

spring.kafka.bootstrap-servers=kafka:9092
```