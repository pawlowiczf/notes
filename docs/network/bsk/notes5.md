## Port-security 

```cisco
Switch(config)# interface fastEthernet 0/1  
Switch(config-if)# switchport mode access           # Ustawia port jako access  
Switch(config-if)# switchport port-security         # Włącza Port Security  
Switch(config-if)# switchport port-security maximum 2 # Maks. 2 adresy MAC  
Switch(config-if)# switchport port-security violation restrict # Reakcja: restrict  
Switch(config-if)# switchport port-security mac-address sticky # Zapisze MAC w konfiguracji 
```

## BPDU Guard 

```cisco
Switch(config)# interface fastEthernet 0/1  
Switch(config-if)# spanning-tree bpduguard enable  
```

```cisco
# Ustawienie portu jako Portfast 
Switch(config)# interface fastEthernet 0/1  
Switch(config-if)# spanning-tree portfast  


# Globalne uruchomienie BPDU Guard dla portów Portfast 
Switch(config)# spanning-tree portfast bpduguard default  
```

## ZBF 

```
Router(config)# zone security INSIDE
Router(config)# zone security OUTSIDE

Router(config)# interface GigabitEthernet0/0
Router(config-if)# zone-member security INSIDE
Router(config)# interface GigabitEthernet0/1
Router(config-if)# zone-member security OUTSIDE

Router(config)# ip access-list extended ALLOW_HTTP
Router(config-ext-nacl)# permit tcp any any eq 8
Router(config)# class-map type inspect CUSTOM_TRAFFIC
Router(config-cmap)# match access-group name ALLOW_HTTP  # Użycie własnego ACL!

Router(config)# policy-map type inspect INSIDE_TO_OUTSIDE
Router(config-pmap)# class type inspect CUSTOM_TRAFFIC
Router(config-pmap-c)# inspect  # Inspekcja stanowa (jak stateful firewall)
Router(config-pmap-c)# exit
Router(config-pmap)# class class-default  # Domyślna klasa (cała reszta ruchu)
Router(config-pmap-c)# drop  # Blokuj niepasujący ruch

Router(config)# zone-pair security INSIDE-OUTSIDE source INSIDE destination OUTSIDE
Router(config-sec-zone-pair)# service-policy type inspect INSIDE_TO_OUTSIDE
```

## Access-listy 

```cisco
# Lista numerowana 
access-list 110 permit tcp 192.168.1.0 0.0.0.255 any eq 80
access-list 110 permit tcp 192.168.1.0 0.0.0.255 any eq 443
access-list 110 deny   ip 192.168.1.5 any
access-list 110 permit ip any any

# Lista nazwana 
ip access-list extended WEB_ACCESS
 permit tcp 10.0.0.0 0.255.255.255 any eq www
 permit tcp 10.0.0.0 0.255.255.255 any eq 443
 deny   tcp host 10.1.1.100 any
 permit ip any any


access-list 10 permit 192.168.1.0 0.0.0.255
access-list 10 deny   192.168.2.1
access-list 10 permit any


# Filtr przepuszczający ruch telnet i ssh do routera R2
# oraz cały ruch IP do innych urządzeń (na wejściu z LAN2)
ip access-list extended F2
 ! Zezwól na ruch SSH (port 22) do routera R2
 permit tcp 192.168.2.0 0.0.0.255 host 192.168.2.1 eq 22
 ! Zezwól na ruch Telnet (port 23) do routera R2
 permit tcp 192.168.2.0 0.0.0.255 host 192.168.2.1 eq 23
 ! Zablokuj pozostały ruch do samego routera R2
 deny   ip any host 192.168.2.1
 ! Zezwól na cały ruch IP do innych urządzeń
 permit ip 192.168.2.0 0.0.0.255 any
```