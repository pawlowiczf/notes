## Dokumentacja API

Skorzystamy z biblioteki `OpenAPI`. Dodaj odpowiednie zależności do pliku z dependencjami. Dokumentacja jest dostępna pod adresem: `localhost:8080/swagger-ui/inidex.html`

### Informacje ogólne o aplikacji 
W głównej klasie (w której zawarta jest metoda `main`) umieszczamy adnotację:
```java
@OpenAPIDefinition(
    info = @Info(
        title = "...",
        description = "...",
        version = "...",
        contact = @Contact(
            name, email, url, ...
        )
    )
)
```

### Informacje o kontrolerze 
Nad klasa kontrolera dodajemy adnotację:
```java
@Tag(
    name = "...",
    description = "..."
)
```

### Informacje o endpoincie 
Nad funkcją obsługującą dany endpoint dodajemy:
```java
@Operation(
    summary = "...",
    description = "..."
)
```

Jeśli chcemy dodać informacje o zwracanych kodach to:
```java
@ApiResponse(
    responseCode = "201",
    description = "..."
)
```

## Inforamcje o DTO 
Możemy dodać przykład wysyłanych danych poprzez dodanie adnotacji do pół DTO:
```java
@Schema(
    description = "...",
    example = "..."
)
```