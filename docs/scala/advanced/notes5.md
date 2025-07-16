
## Monada 

Monada to abstrakcyjny typ danych. Aby coś było monadą, musi spełniać pewne właściwości (prawa monadyczne) i mieć określoną strukturę.

Dla typu `M[A]`, żeby był monadą, muszą istnieć:

1. funkcja `pure`, która opakowuje wartość w kontekst monadyczny:   
```def pure[A](a: A): M[A]```
2. funkcja `flatMap`, która pozwala spłaszczyć sekwencję obliczeń:   
```def flatMap[A](ma: M[A])(f: A => M[B]): M[B]```

Musi spełniać też prawa monadyczne:  

1. Prawo lewej tożsamości ```pure(a).flatMap(f) == f(a)```
2. Prawo prawej tożsamości ```m.flatMap(pure) == m```
3. Prawo łączności ```m.flatMap(f).flatMap(g) == m.flatMap(x => f(x).flatMap(g))```

