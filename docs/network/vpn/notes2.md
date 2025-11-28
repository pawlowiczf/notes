# IPSec

IPSec to zestaw protokołów: 

1. AH - authentication header (koncentruje się na nagłówku, integralność całego pakietu IP, uwierzytelnienie nadawcy)
2. ESP - encapsulating security payload (koncentruje się na danych, szyfrowanie danych, integralność danych)

Tak, to nazewnictwo jest słabe, ale to są nazwy protokołów. 

Tryby pracy IPSec:
Tryb tunelowy - połączenie miejsce-miejsce, oddział-oddział
Tryb transportowy - telepracownik-firma

Tryb transportowy ESP pomiędzy dwoma hostami:
`[IP][ESP header][zaszyfrowany payload][ESP trailer][ESP auth]`

Tryb tunelowy ESP pomiędzy routerami:
`[Nowy IP][ESP header][zaszyfrowany: stary IP + payload][ESP trailer][ESP auth]`

W trybie tunelowym, oryginalny nagłówek IP jest zaszyfrowany (także z danymi). IPSec dodaje nowy nagłówek. W trybie transportowym, dane są zaszyfrowane, ale oryginalny nagłówek już nie. Routery pośrednie widzą zatem adres docelowy każdego pakietu. 

ISAKMP - Internet Security Association and Key Management Protocol - zestaw protokołów wykorzystywany przez IPSec, definiujący mechanizmy wymiany kluczy oraz negocjacji.

AH chroni również nagłówek IP, co powoduje problemy z NAT (zła suma kontrolna). Z tego powodu AH nie jest praktycznie wykorzystywane w VPNach. 


```
// dodanie esp-sha-hmac oznacza dodanie ESP i AH
crypto ipsec transform-set FOR_WIN10 esp-3des esp-sha-hmac
 mode transport
```

Ważne, żeby końce tunelowe były dla siebie widoczne, dostępne - muszą być dobrze zdefiniowane tablice routingu.

```
show crypto isakmp policy
show crypto ipsec transform-set
show crypto ipsec sa
show crypto map
show access-lists
```

```
access-list 1 permit 10.0.1.0 0.0.0.255
access-list 100 deny ip 10.0.1.0 0.0.0.255 any
access-list 100 permit gre host 200.0.0.2 host 200.0.0.1
access-list 100 deny gre any any
access-list 100 permit ip any any

ip nat inside
ip nat outside

ip nat inside source list 1 interface GigabitEthernet0/0/1 overload
```