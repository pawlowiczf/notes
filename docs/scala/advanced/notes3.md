## Partially Applied Functions 

`Currying` to technika przekształcania funkcji przyjmującej wiele argumentów w sekwencję rodzin funkcji, z których każda przyjmuje jeden argument.

```scala title="Dwa przykłady rozwijania funkcji"
def concat(a: String, b: String, c: String): String = a + b + c

val `concat-b-paf`: String => String => String = 
    a => c => concat(a, "here", c)
val `concat-b-paf2` = 
    concat(_: String, "here", _: String)
```