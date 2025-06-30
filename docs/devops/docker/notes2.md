
### Podstawowe informacje 

Stwórz plik `docker-compose.yml`. Prosty przykładowy plik wygląda następująco:

```yaml
services:
    backend:
        # sami budujemy obraz 
        build: 
            context: ./backend 
            dockerfile: Dockerfile 
        container_name: backend 
        ports:
            - "8080:8080"
        environment:
            SPRING_DATASOURCE_URL: jdbc:postgresql://db:5432/backend-db
        depends_on:
            db:
                condition: service_healthy 
                restart: true 
        networks:
            - app-network 

    db:
        # korzystamy z gotowego obrazu
        image: postgres
        healthcheck:
            test: ["CMD-SHELL", "pg_isready -U admin -d backend-db"]
            interval: 10s
            retries: 5
            start_period: 30s
            timeout: 10s    

networks:
    app-network:
        driver: bridge

```

W celu startu wykonaj: `docker compose up -d`, gdzie flaga `-d` oznacza opcję `detach` - odpalenia w tle. 

Można też zatrzymać lub ponownie wystartować kontenery:
```sh
docker compose stop
docker compose start 

docker compose down 
```

### Zmienne środowiskowe 

Docker Compose szuka pliku `.env` - eksportuje znajdujące się tam zmienne środowiskowe. Można też jawnie przekazać scieżkę do pliku:
```sh
docker compose --env-file ./myenv up -d
```

Docker Compose widzi też zmienne środowiskowe ustawione z zewnątrz. Przykładowo, w Github Actions można użyć kluczy repozytorium:

```sh
run: |
    export AWS_SECRET_ACCESS_KEY=${{ secrets.AWS_SECRET_ACCESS_KEY }}
    docker compose up -d
```