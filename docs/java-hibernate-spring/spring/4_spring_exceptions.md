---
title: Spring Exceptions
layout: default
---

## Obsługiwanie wyjątków

Tworzymy własny wyjątek i własną klasę DTO, która będzie przesyłana klientowi. Przykładowo:

```java
class StudentNotFoundException extends RunTimeException {}

class StudentErrorResponse {
    int status;
    String message;
    Timestamp timestamp;
}
```

W celu zarejestrowania obsługi wyjątku tworzymy funkcję z adnotacją `@ExceptionHandler`:
```java
@ExceptionHandler
ResponseEntity<StudentErrorResponse> handleStudentNotFoundException(StudentNotFoundException ex) {
    StudentErrorResponse error = new StudentErrorResponse()
    error.setStatus(HttpStatus.NOT_FOUND.value());
    ... 
    return new ResponseEntity<>(error, HttpStatus.NOT_FOUND);
}
```

Funkcje handlujące można dodawać wewnątrz kontrolerów - wtedy to jest ich zakres. 

## Globalna obsługa wyjątków

Gdy potrzebujemy obsługiwać błąd dla wielu kontrolerów, możemy stworzyć globalną obsługę wyjątku. Taką funkcję obsługującą umieszczamy wewnątrz klasy, oznaczonej adnotacją `ControllerAdvice`. Przykładowo:
```java
@ControllerAdvice
public class StudentRestExceptionHandler {}
```

## Obsługa błędów przy walidacji 
W przypadku używania adnotacji np. `@NotBlank, @NotEmpty(message = ...), @Min`, jeśli zapytanie nie spełnia wymagań, zostanie rzucony błąd: 

1. `MethodArgumentNotValidException` - dla ciała żadania z adnotacją `@RequestBody`
2. `ConstraintViolationException` - dla parametrów z walidacją na poziomie metod lub query params. 

Trzeba je przechwywić w globalnym handlerze wyjątków. Przykładowo:
```java
@ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidationExceptions(MethodArgumentNotValidException ex) {}
```