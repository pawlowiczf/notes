## Type generyczne


## Obiekty cech 

Powiedzmy, że mamy interfejs o nazwie `GameElement` z metodą `process()`. **Nie jest możliwe stworzenie wektora** `Vec<GameElement>`, ponieważ nie jest znany rozmiar typów, implementujących tego trait'a. 

W celu obejścia, stosuje się inteligentne wskaźniki:
```rust 
Vec<Box<dyn GameElement>> = vec!();
```