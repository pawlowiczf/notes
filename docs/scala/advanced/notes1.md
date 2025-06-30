

### Methods with one argument 

Do metod/funkcji, które przyjmują jeden argument, można wstawić cały blok kodu (ostatnia wartość to będzie przekazany argument). Robi się to następująco:

```scala
val aResult = singleArgMethod({
    // long code 
    42 
})
```

Jest tu troszkę dużo nawiasów, dlatego można to uprościć następująco:
```scala
val aResult = singleArgMethod {
    // long code 
    42 
}
```

### Single abstract method pattern 

Interfejsy, które posiadają tylko jedną metodą bez implementacji, dają się łatwo implementować, w stylu podobnym do deklaracji funkcji anonimowych. 

Przykładowo, stwórzmy wątek, który implementuje interfejs `Runnable` z jedną metodą `run`:

```scala
// ugly code
val aThread = new Thread(new Runnable {
    override def run(): Unit = println("...")
})

// elegant and transparent code
val bThread = new Thread(() => println("..."))
```

### Methods ending in a `:`
Metody (klas/interfejsów), które nie zawierają znaków alfanumerycznych i kończą się na `:` są *right-associative*. Przykładem jest wyrażenie np. `1 :: 2 :: 3 :: List(4)` - które na liście wykonuje: `List(4).::3.::2.::1`. 

### Multi word identifiers 
Nazwy funkcji, pól, klas, obiektów mogą składać się z wielu słów (lecz muszą być zawarte w backtickach). Przykładowo:

```scala
def `and then said`() = println("...")

object `Content-Type` {
    val `application/json` = "application/JSON"
}
```

### Variable arguments 

```scala
def methodWithVarargs(args: Int*) = { }

methodWithVarargs(1)
methodWithVarargs(1,2)
methodWithVarargs(1,3,4,5)

val aCollection = List(1,2,3,4)
methodWithVarargs(aCollection*)
```