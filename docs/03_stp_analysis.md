# Analiza Spanning Tree

Topologia: trójkąt SW1 – SW2 – SW3  
Protokół: Rapid PVST+  

```text
     [SW1]==== Port-channel1 ====[SW2]
       \                           /
      Gi0/1                      Gi0/1
         \                       /
        Gi0/0                 Gi0/1
                  [SW3]
```

---

## 1. Podział Root Bridge

Zamiast jednego głównego switcha dla całej sieci, role podzielono na dwa urządzenia w celu rozłożenia ruchu:

- SW1: Root Primary dla VLAN 10 i 99, Root Secondary dla VLAN 20 i 30
- SW2: Root Primary dla VLAN 20 i 30, Root Secondary dla VLAN 10 i 99

Priorytety:
- Root Primary: 24576 + numer VLAN
- Root Secondary: 28672 + numer VLAN

Warto pamiętać, że w topologii Router-on-a-Stick protokół STP optymalizuje wyłącznie ruch wewnątrz tego samego VLAN-u. Cały ruch między różnymi podsieciami i tak musi przejść przez router.

---

## 2. Stany portów na SW3

Dla VLAN 10 i 99:
- Gi0/0 do SW1: Root FWD
- Gi0/1 do SW2: Altn BLK

Dla VLAN 20 i 30:
- Gi0/0 do SW1: Altn BLK
- Gi0/1 do SW2: Root FWD
- Gi0/2 do VPC: Desg FWD

Ten sam fizyczny kabel SW3–SW1 zmienia stan z FWD na BLK w zależności od VLAN-u, dzięki czemu ruch L2 nie idzie tylko jednym łączem.
