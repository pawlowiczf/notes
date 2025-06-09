## Obsługa błędów

Mamy dwa rodzaje błędów: błędy nienaprawialne i błędy do obsłużenia. 

W celu natychmiastowego zakończenia programu błędem, wykorzystujemy makro `panic!()`. Przykładowo:
```rust
fn test_panic() {
    panic!("cannot connect to database");
}
```

## Błędy do obsłużenia 

Druga kategoria to błędy do obsłużenia. W Rust nie ma wyjątków i bloków `try/catch`, gdyż takie rozwiązanie jest wolne - tworzy alternatywną ścieżkę programu. Lepiej, po prostu, zwracać możliwy błąd, jako rezultat wywołania funkcji. Został stworzony `enum`:
```rust
enum Result<T, E> {
    Ok(T),
    Err(E)
}
```

Po wywołaniu takiej funkcji możemy rozpakować wartość na kilka sposobów. Przykładowo:
```rust
let result = Rectangle::new(1.0, -1.0);
let rectangle = match result {
    Ok(rec) => rec,
    Err(msg) => {
        println!("{}", msg);
        Rectangle {width: 1.0, height: 1.0}
    }
};
```

Innym sposobem jest skorzystanie z funkcji na strukturze `Result`:
```rust
result.unwrap()
result.expect("...")
result.unwrap_or_else(|_| Rectangle {...})
result.unwrap_or_default(); // T must implement Default trait
```

## Modyfikacje 
Jeśli funkcja, którą wywołuję w innej funkcji także zwraca enuma `Result`, to nie muszę go rozpakowywać i zwracać błędu, lecz wykorzystać gotowy mechanizm. 

```rust
fn bigger(...) -> Result<Rectangle, String> {
    let r = Rectangle::new(1.0, -1.0)?; 
}

// Using ? is equal to writing the code below: 
let r = match r {
    Ok(r) => r,
    Err(e) => return Err(e) 
};
```