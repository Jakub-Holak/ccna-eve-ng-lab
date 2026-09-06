# Usługi Sieciowe

Zakres: DHCP, NAT/PAT, SVI oraz NTP

---

## 1. Serwer DHCP na R1

Router brzegowy R1 obsługuje automatyczne przydzielanie adresów IP dla stacji roboczych:
- Pula POOL_VLAN10: sieć 172.16.0.0/24, brama 172.16.0.1, DNS 8.8.8.8
- Pula POOL_VLAN20: sieć 172.16.1.0/26, brama 172.16.1.1, DNS 8.8.8.8
- Wykluczenia: pierwsze 10 adresów w każdej podsieci zostało wyłączone z puli w celu zabezpieczenia bram domyślnych oraz stałych adresów urządzeń sieciowych.

Prawidłowy przebieg procesu D.O.R.A. zweryfikowano na stacjach roboczych oraz w tabeli powiązań poleceniem `show ip dhcp binding`.

---

## 2. NAT / PAT na R1

Dostęp do sieci publicznej dla całej przestrzeni prywatnej 172.16.0.0/22 realizowany jest za pomocą translacji Port Address Translation z przeciążeniem adresu na porcie Gi0/2:
- Interfejsy wewnętrzne (inside): subinterfejsy dot1Q oraz łącze P2P Gi0/0 (umożliwiające translację ruchu od klientów dochodzących przez router R2).
- Interfejs zewnętrzny (outside): port Gi0/2 skierowany do operatora WAN.

Poprawność translacji potwierdzono weryfikacją tabeli translacji poleceniem `show ip nat translations`.

---

## 3. Adresy zarządzania switchy – SVI

Każdy przełącznik posiada skonfigurowany wirtualny interfejs w dedykowanym VLAN-ie 99 wraz ze wskazaną bramą domyślną:

| Switch | Interfejs | Adres IP | Brama domyślna |
|---|---|---|---|
| SW1 | Vlan99 | 172.16.1.101/28 | 172.16.1.97 |
| SW2 | Vlan99 | 172.16.1.102/28 | 172.16.1.97 |
| SW3 | Vlan99 | 172.16.1.103/28 | 172.16.1.97 |

---

## 4. NTP

- Router brzegowy R1 działa jako wzorzec czasu (ntp master 2).
- Router R2 oraz switche SW1, SW2, SW3 synchronizują zegary systemowe z adresem zarządzania R1 (172.16.1.97).

Pełne polecenia usług znajdują się w plikach konfiguracyjnych w katalogu `configs/`.
