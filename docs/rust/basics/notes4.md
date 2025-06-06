# Czas życia 

## Czas życia parametrów funkcji 

Kiedy funkcja zwraca pożyczkę, to nie wiemy, czyja i kogo jest to pożyczka. Musimy wprowadzić pewne ograniczenia na czas życia tej pożyczki. Przykładowo: pożyczka ma żyć tak długo, jak najkrótszy czas życia parametrów funkcji. 

```rust
fn longer_array<'a>(a : &'a[i32], b : &'a[i32]) -> &'a[i32] {
    if a.len() > b.len() {
        a
    } else {
        b
    }
}

// jeśli a lub b odda własność, pożyczka przestanie być poprawna. 
```