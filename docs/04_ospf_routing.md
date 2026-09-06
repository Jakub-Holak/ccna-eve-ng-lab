# Routing OSPFv2

Protokół: OSPFv2 Single-Area 0  
Połączenie: R1 – R2 przez łącze tranzytowe P2P 172.16.1.112/30 na portach Gi0/0  

---

## 1. Identyfikacja i polityka rozgłaszania

- R1: router-id 1.1.1.1
- R2: router-id 2.2.2.2

W procesie OSPF zastosowano zasadę `passive-interface default`, wyciszając pakiety Hello na wszystkich interfejsach. Jedynym wyjątkiem jest port Gi0/0 łączący oba routery. 

Dzięki temu:
- Podsieci użytkowników i serwerów są w pełni rozgłaszane w domenie OSPF jako sieci końcowe.
- Routery nie emitują zbędnego ruchu multicast w stronę switchy dostępowych.
- Zabezpieczono domenę przed wstrzyknięciem nieautoryzowanego routera w sieciach lokalnych.

Pełne polecenia konfiguracji procesu OSPF znajdują się w plikach `configs/R1_startup.cfg` oraz `configs/R2_startup.cfg`.

---

## 2. Propagacja trasy domyślnej

Router brzegowy R1 posiada statyczną trasę domyślną skierowaną do operatora WAN (203.0.113.1).  
Za pomocą polecenia `default-information originate` trasa ta jest dynamicznie wstrzykiwana do procesu OSPF.

Wynik weryfikacji na routerze R2:
```text
Gateway of last resort is 172.16.1.113 to network 0.0.0.0
O*E2  0.0.0.0/0 [110/1] via 172.16.1.113, GigabitEthernet0/0
```

---

## 3. Wyniki weryfikacji łączności

- Sąsiedztwo na łączu P2P osiągnęło stabilny stan FULL.
- Baza stanów łączy (LSDB) na obu routerach jest w pełni zsynchronizowana.
- Komunikacja między odległymi VLAN-ami działa bez zakłóceń.
- Hosty zza routera wewnętrznego R2 docierają do Internetu pokonując trasę przez R2, łącze P2P oraz router brzegowy R1.
