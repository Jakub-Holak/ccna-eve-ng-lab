# Tabela VLAN-ów i Trunkingu

## 1. Konfiguracja VLAN-ów

| VLAN ID | Nazwa | Podsieć | Brama R1 | Brama R2 | Przeznaczenie |
|---|---|---|---|---|---|
| 10 | Users | 172.16.0.0/24 | 172.16.0.1 | 172.16.0.2 | Hosty użytkowników VPC6 |
| 20 | IT | 172.16.1.0/26 | 172.16.1.1 | 172.16.1.2 | Dział IT |
| 30 | Servers | 172.16.1.64/27 | 172.16.1.65 | 172.16.1.66 | Serwery |
| 99 | Management | 172.16.1.96/28 | 172.16.1.97 | 172.16.1.98 | Zarządzanie urządzeniami SVI |

Łącze P2P R1–R2 na porcie Gi0/0 działa jako zwykły port L3 bez tagowania VLAN.

---

## 2. Zasady trunkingu

- Między switchami: magistrale 802.1Q włączone na wszystkich łączach wewnętrznych.
- Między switchami a routerami: port switcha w trybie trunk, router obsługuje ruch przez subinterfejsy 802.1Q.
- Native VLAN: identyfikator 99 ustawiony spójnie na każdym trunku w sieci. Ruch nietagowany trafia bezpośrednio do strefy zarządzania.

---

## 3. Architektura Router-on-a-Stick

Ruch między VLAN-ami jest przekazywany do routerów przez subinterfejsy logiczne na porcie Gi0/1:
- Gi0/1.10 – obsługa ruchu VLAN 10
- Gi0/1.20 – obsługa ruchu VLAN 20
- Gi0/1.30 – obsługa ruchu VLAN 30
- Gi0/1.99 – obsługa ruchu VLAN 99 z flagą native

Pełne pliki konfiguracyjne urządzeń znajdują się w katalogu `configs/`.
