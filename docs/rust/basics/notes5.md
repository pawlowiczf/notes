# Enumy 

Enumy są bardzo potężne. Każde pole w enumie zachowuje się jak struktura - może zawierać w sobie też inne enumy, czy struktury. Dla enumów można też definiować funkcje w bloku `impl`. 

Oto przykładowe tworzenie złożonych enumów:

```rust
struct Button {
    color : String,
    background_color : String,
    border_radius : i8,
}

enum Direction {
    UP, DOWN, LEFT, RIGHT 
}

enum UIEvent {
    ButtonClicked(Button), Scroll(Direction), KeyPressed(char)
}

fn main() {
    let clicked = UIEvent::ButtonClicked(Button{
        color: "red".to_string(),
        background_color: "white".to_string(),
        border_radius: 15,
    });
    
    let scroll = UIEvent::Scroll(Direction::UP);
}
```