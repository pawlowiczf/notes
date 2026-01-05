
### Podstawowe elementy tworzenia pliku

```dockerfile
# Wybiera obraz bazowy
FROM openjdk:21-slim

# Ustawia biężący katalog roboczy wewnątrz kontenera. Wszystkie polecenia (COPY, RUN, ...)
# wykonywane są właśnie w tym katalogu.
WORKDIR /app

# Ustawia zmienną środowiskową
ENV DB_URL=...
ENV APP_NAME=hello-world
ENV SPRING_PROFILES_ACTIVE=deployment

# Kopiuje plik z lokalnego kontekstu budowania do kontenera do folderu ./target
# zmieniając też nazwę pliku na app2.jar
COPY app.jar ./target/app2.jar
```

```dockerfile
FROM maven:3.9.8-eclipse-temurin-21

```

### Multi-stage build

Multi-stage build to technika, która pozwala tworzyć obrazy w kilku etapach, co umożliwia
- kompilowanie aplikacji w jednym etapie (z wszystkimi potrzebnymi narzędziami)
- skopiowanie tylko gotowych plików binarnych/aplikacyjnych do końcowego, lekkiego obrazu produkcyjnego

```dockerfile
# Etap 1: budowanie aplikacji
FROM maven:3.9-eclipse-temurin-17 AS builder
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn clean package -DskipTests

# Etap 2: tylko gotowy JAR w lekkim obrazie
FROM eclipse-temurin:17-jre AS runtime
WORKDIR /app

# Kopiujemy rezultat z poprzedniego etapu
COPY --from=builder /app/target/my-app.jar ./app.jar
CMD ["java", "-jar", "app.jar"]
```

### Różnice między CMD/RUN/ENTRYPOINT

`https://www.docker.com/blog/docker-best-practices-choosing-between-run-cmd-and-entrypoint/`

### Budowanie i pushowanie
Zbudujemy obraz i dodamy go do zdalnego rejestru na DockerHub:
```sh
docker build -t username/repo:tag backend/Dockerfile .
docker image push docker.io/username/repo:tag
```