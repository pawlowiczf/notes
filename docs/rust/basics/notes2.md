# Programowanie obiektowe 

## Definiowanie struktur 

```rust title="Przykłady prostego definiowania struktur"
struct Color; 
struct Point(u32, u32, u32); 
```

```rust title="Przykład definicji złożonej struktury"
#[derive(Debug)] // allows to print instance
struct Spaceship {
    name : String,
    x : f32, 
    y : f32, 
    direction : f32,
    speed : i32
};

let ship = Spaceship {
    name: "Avalanche".to_string(),
    x : 4.0,
    y : 4.0,
    direction : 45.0,
    speed: 10 
};

// let mut ship = Spaceship {...}; 
```

## Definiowanie metod dla struktur
Metody definiuje się w bloku `impl`:
```rust
impl Spaceship {
    fn who_am_i(&self) -> &str { // borrow value 
        self.name.as_str()
    }

    fn destroy(self) { // move value 
        println("Spaceship {} destroyed", &self.name)
    }
}
```

Metody bez argumentu `self`, określane są jako statyczne (nie instancyjne):
```rust
impl Spaceship {
    fn spawn(name : String) -> Spaceship {
        ...
    }
}

let ship = Spaceship::spawn("Destroyer".to_string());
```

## Cechy (traits)
Cechy to takie interfejsy. Dodają warstwę abstrakcji nad strukturami. Tworzenie i implementacja wygląda następująco:

```rust
trait PositionedElement {
    fn pos(&self) -> (f32, f32)
}

impl PositionedElement for Spaceship {
    fn pos(&self) -> (f32, f32) {
        (self.x, self.y)
    }

    fn is_collided(&self, other : &impl PositionedElement) -> bool {
        // it is possible to have a default implementation
        ...
    }
}
```
