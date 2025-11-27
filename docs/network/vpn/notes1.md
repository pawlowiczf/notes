# GRE - Generic Routing Encapsulation

GRE to tunelujący protokół kapsułkujący różne protokoły w prostą ramkę IP, umożliwiając przesyłanie ruchu pomiędzy punktami oddzielonymi logicznie lub fizycznie. 

Plan działania: 

1. Router GRE przy tunelu wejściowym bierze oryginalny pakiet
2. Dokleja nagłówek GRE
3. Dokleja nowy nagłówek IP z adresem źródłowym i docelowym interfejsów tunelowych
4. Pakiet podróżuje Internetem, jak zwykły ruch IP
5. Router przy tunelu wyjściowym zdejmuje nagłówki GRE+IP i przekazuje pakiet dalej jak lokalny

!!! note warning "Brak szyfrowania"

    GRE nie nadaje się do bezpiecznych kanałów - łączy się je z IPsec (tzw. GRE over IPsec)

Stworzony tunel tworzy nową, logiczną sieć, która posiada własną adresację. Przykładowo, jeden z routerów konfigurujemy następująco:

```cisco
interface tunnel 0
    tunnel mode gre ip
    tunnel source fa0/1 // początek tunelu, zewnętrzny interfejs routera
    tunnel destination 200.0.0.2 // koniec tunelu (rzeczywisty adres IP)
    ip address 10.0.42.1 255.255.255.0 // adresacja w nowej sieci 
```

Podobnie konfigurujemy drugi router. Na razie komunikacja nie jest jeszcze możliwa. Każdy z routerów potrzebuje trasy statycznej do drugiej sieci. Przykładowo:

```cisco
// Ruch do sieci 10.0.2.0, kieruj na drugi koniec tunelu, tj. 10.0.42.2
ip route 10.0.2.0 255.255.255.0 10.0.42.2
```

![Generic Routing Encapsulation scheme](../images/gre.PNG)