## Pattern matching (apply, unapply)

Nadpisanie metody `apply` w obiekcie towarzyszącym 

```scala

object Person {
    def apply(name: String, age: Int) = new Person(name, age)
    def apply(age: Int) = new Person("Karol", age)
}
```

## Partial functions

## Functions extending `(A => B)`

Wiele kolekcji rozszerza `(A => B)`, czyli dziedziczy po `Function1[A, B]`. Można tworzyć własne klasy, które też to robią. Oznacza to, że instancje tej klasy mogą być traktowane jak funkcje, tzn. mogą być wywoływane jak `object(argument)`. Przykładowo:

```scala
// In this case, overridden method apply, returns boolean, if an element belongs to set:
val aSet = Set(1,2,3,4)
aSet(1) // returns true 
aSet(9) // returns false 
```

```scala
class Person(val name: String, val age: Int) extends (() => String) {
    override def apply() =
        if age >= 18 then "allowed to drink"
        else "not allowed to drink"
}

val person = new Person("Tom", 15)
person() // returns "not allowed to drink"
```

To nadpisanie metody `apply` określa zachowanie obiektu podczas wywołania. 
