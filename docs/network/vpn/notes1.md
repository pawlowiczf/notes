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

