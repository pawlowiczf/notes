# eBGP

EGP - Exterior Gateway Protocols

Systemy autonomiczne:
- są niezależnie zarządzane
- stosują własny routing wewnętrzny
- definiują własną politykę bezpieczeństwa
- są numerowane (1-65535)

Internet może być traktowany jako zbiór systemów autonomicznych, połączonych między sobą. Zadaniem BGP jest zapewnić routing pomiędzy systemami autonomicznymi. 

BGP speaker - router z uruchomionym BGP
Peer - sąsiad (w tym samym AS: internal peer, w innym AS: external peer)

Odległosć jest wyrażona w sekwencji AS (AS_PATH). Jeśli nasz system jest już na trasie, to coś jest nie tak. Odległośc nie jest najważniejszym czynnikiem w wyborze trasy.

Ważne: sieć musi być obecna w tabeli routingu routera (np. przez IGP lub statyczną trasę), inaczej BGP jej nie rozgłosi.