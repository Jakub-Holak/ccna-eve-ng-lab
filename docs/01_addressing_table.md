# Tabela adresacji IP – VLSM

Pula bazowa: 172.16.0.0/22  
Styk z operatorem: 203.0.113.0/30  

---

## 1. Podział podsieci

| Podsieć | Maska | Zakres hostów | Broadcast | Przeznaczenie | Bramy R1 / R2 |
|---|---|---|---|---|---|
| 172.16.0.0/24 | 255.255.255.0 | 172.16.0.1 – 172.16.0.254 | 172.16.0.255 | VLAN 10 – Użytkownicy | R1: .0.1 / R2: .0.2 |
| 172.16.1.0/26 | 255.255.255.192 | 172.16.1.1 – 172.16.1.62 | 172.16.1.63 | VLAN 20 – Dział IT | R1: .1.1 / R2: .1.2 |
| 172.16.1.64/27 | 255.255.255.224 | 172.16.1.65 – 172.16.1.94 | 172.16.1.95 | VLAN 30 – Serwery | R1: .1.65 / R2: .1.66 |
| 172.16.1.96/28 | 255.255.255.240 | 172.16.1.97 – 172.16.1.110 | 172.16.1.111 | VLAN 99 – Zarządzanie | R1: .1.97 / R2: .1.98 |
| 172.16.1.112/30 | 255.255.255.252 | 172.16.1.113 – 172.16.1.114 | 172.16.1.115 | Łącze P2P R1–R2 | R1: .1.113 / R2: .1.114 |
| 203.0.113.0/30 | 255.255.255.252 | 203.0.113.1 – 203.0.113.2 | 203.0.113.3 | Styk z operatorem WAN | R1: .113.2 / ISP: .113.1 |

Podsieci przylegają do siebie bez luk i nakładania się adresów.  
W podsieci /28 adres .111 to broadcast, ostatni host to .110.

---

## 2. Przypisanie do interfejsów

| Podsieć | Interfejs R1 | Interfejs R2 | Interfejs switchy |
|---|---|---|---|
| 172.16.0.0/24 | Gi0/1.10 | Gi0/1.10 | – |
| 172.16.1.0/26 | Gi0/1.20 | Gi0/1.20 | – |
| 172.16.1.64/27 | Gi0/1.30 | Gi0/1.30 | – |
| 172.16.1.96/28 | Gi0/1.99 | Gi0/1.99 | Vlan99 SVI: SW1 .101, SW2 .102, SW3 .103 |
| 172.16.1.112/30 | Gi0/0 | Gi0/0 | – |
| 203.0.113.0/30 | Gi0/2 | – | – |
