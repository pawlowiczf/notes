## Lazy evaluation

```scala
lazy val age = {
    println("Computing age...")
    42
}
// we won't see any output, unless we use `age` variable somewhere 
```

## Call-by-name

Wyrażenie `random()` nie jest ewaluowane - kompilator zamienia je na `() => random()`. Co więcej, w ciele funkcji, możemy odwołać się do zawartości pisząc `x` - kompilator zamienia to na `x.apply()`, czyli `x()`. 

```scala
def printTwice(x: => Int) = {
    println(x)
    println(x)
}

def random(): Int = {
    Random.nextInt(100)
}

printTwice(random())
// prints two different numbers
```

## Call-by-need

Możemy połączyć dwa powyższe podejścia i stworzyć funkcję, która przyjmuje leniwe obliczenie (ale go nie wywołuje), nastepnie tworzy leniwą zmienną z wartością zwracaną przez podaną funkcję. To podejście łączy wydajność z leniwością. 

```scala title="Leniwa ewaluacja na dwa sposoby - róznice w wywołaniach/dostępie"
def byNeedA(n: => Int) = 
    lazy val lazyN = n // cache 
    lazyN + lazyN + 1 

def byNeedB(n: () => Int) = 
    lazy val lazyN = n()
    lazyN + lazyN + 1 

byNeedA(getNumber())
byNeedB(getNumber)
```

Lek. 10-12