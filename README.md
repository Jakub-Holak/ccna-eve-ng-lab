**CCNA HOME Lab - EVE-NG Network Simulation**

**Środowisko laboratoryjne symulujące sieć korporacyjną, zbudowane w EVE-NG z wykorzystaniem obrazów Cisco vIOS. Projekt realizuje pełen zakres tematyczny egzaminu CISCO CCNA 200-301.**

<br>

**Spis treści:**

O projekcie

Topologia

Technologie

Fazy projektu

Struktura repozytorium

<br>

**O projekcie:**

Projekt ma dwa cele:
1. Edukacyjny - praktyczne wdrożenie każdego modułu egzaminu CCNA 200-301 w środowisku laboratoryjnym.
2. Portfolio - zademonstrowanie umiejętności projektowania, konfiguracji i zabezpieczania sieci korporacyjnej - enterprise.

**Sieć odzwierciedla typową architekturę średniej firmy:**
strefa użytkowników,
serwerów,
segment zarządzania,
IT,
połączenie z symulowanym internetem

<br>

**Topologia**
```
					            Internet
						            |
			   Gi0/0-----------------------------------Gi0/0
                 R1  				                     R2
               Gi0/1  				                   Gi0/1          
                 |				                         |
			   Gi0/0 				                   Gi0/0	              
    ------Gi0/2 SW1 Gi0/3-------------------------Gi0/3 SW2 Gi0/2------
	|				Gi1/0-------------------------Gi1/0			      |                     
  VPC6	  	   Gi0/1		     		               Gi0/1         VPC8
                 |	                                     |           
                 ---------Gi0/0 SW3 Gi0/1-----------------
					           Gi0/2
                                 |
                                VPC7


Trójkąt STP: SW1 <-> SW2
              |       |
              -> SW3 <-
```

<br>

**Urządzenia (nazwa - model - rola):**

R1 - Cisco vIOS - Router brzegowy, DHCP, NAT/PAT, NTP master, OSPF

R2 - Cisco vIOS - Router dystrybucji, router wewnętrzny

SW1 - Cisco vIOS-L2 - Root Bridge dla VLANu 10 i 99, EtherChannel

SW2 - Cisco vIOS-L2 - Root Bridge dla VLANu 20 i 30, Dystrybucja, EtherChannel

SW3 - Cisco vIOS-L2 - Dostęp (Access layer)

VPCx3 - EVE-NG VPC - Hosty testowe

<br>

**Technologie:**

**Kategoria**		**Stack**

Hiper nadzorca   	VMware (NAT adapter)

Emulator sieci	 	EVE-NG Community

Obrazy			    Cisco vIOS (QEMU), Cisco vIOS-L2 (QEMU)

Transfer plików		SFTP (przez WinSCP)

Konsola CLI 		PuTTY

<br>

**Fazy projektu:**

<br>

**(Zakończone)**

**Faza 1 - Środowisko i warstwa fizyczna**

**Przygotowanie infrastruktury:**

Konfiguracja karty sieciowej VMware

Transfer obrazów Cisco vIOS przez SFTP (zadbanie o prawidłową strukturę katalogów vios-, viosl2-)

Naprawa uprawnień (fixpermissions)

Wdrożenie topologii: 2 routery, 3 switche, hosty VPC

Integracja konsoli PuTTY z Windows

<br>

**(Zakończone)**

**Faza 2 - Fundamenty i adresacja IP**

**Projektowanie schematu adresacji metodą VLSM:**

| Podsieć | VLAN | Maska | Zakres hostów | Bramy (R1 / R2) | Rola / Przeznaczenie |
|---|---|---|---|---|---|
| `172.16.0.0/24` | 10 | `/24` (`255.255.255.0`) | `.0.1 – .0.254` | R1: `.0.1` / R2: `.0.2` | Użytkownicy końcowi (Users) |
| `172.16.1.0/26` | 20 | `/26` (`255.255.255.192`) | `.1.1 – .1.62` | R1: `.1.1` / R2: `.1.2` | Dział IT |
| `172.16.1.64/27` | 30 | `/27` (`255.255.255.224`) | `.1.65 – .1.94` | R1: `.1.65` / R2: `.1.66` | Serwery produkcyjne |
| `172.16.1.96/28` | 99 | `/28` (`255.255.255.240`) | `.1.97 – .1.110` | R1: `.1.97` / R2: `.1.98` | Zarządzanie urządzeniami (Management SVI) |
| `172.16.1.112/30` | – | `/30` (`255.255.255.252`) | `.1.113 – .1.114` | R1: `.1.113` / R2: `.1.114` | Łącze tranzytowe P2P (R1 ↔ R2) |
| `203.0.113.0/30` | – | `/30` (`255.255.255.252`) | `.113.1 – .113.2` | R1: `.113.2` / ISP: `.113.1` | Styk z operatorem (Public WAN / ISP) |

**Wdrożone podstawowe "Quality of Life":**

logging synchronous

no ip domain-lookup

hostname ………………… (w zależności od urządzenia)

<br>

**(Zakończone)**

**Faza 3 - Switching (moduł B CCNA)**

VLANy: VLAN 10,20,30,99 na przełącznikach

Access/Trunk: Porty dostępowe dla VPC, łącza 802.1Q trunk między urządzeniami L2/L3

STP/RSTP: Analiza topologii, ręczna manipulacja priorytetami -> SW1 jako Root Bridge dla VLAN 10 i 99 oraz SW2 jako Root Bridge dla VLAN 20 i 30, migracja do RSTP (rapid-pvst)

EtherChannel (LACP): Agregacja dwóch łączy między SW1 a SW2 dla redundancji i przepustowości

<br>

**(Zakończone)**

**Faza 4 - Routing (moduł C CCNA)**

Routing między VLANami (Router-on-a-stick): subinterfejsy Gi0/1.10, Gi0/1.20, Gi0/1.30

Routing statyczny: Default Route na routerze brzegowym

OSPFv2 (single-area): Automatyczna wymiana tras między R1 i R2

<br>

**(Zakończone)**

**Faza 5 - Usługi sieciowe (moduł D CCNA)**

DHCP: Serwer DHCP na routerze, automatyczne przydzielanie adresów

NAT/PAT: Translacja 172.16.0.0/22 na jeden publiczny IP na R1

NTP: Synchronizacja czasu między urządzeniami (pod analizę logów)

<br>

**(Zakończone)**

**Faza 6 - Bezpieczeństwo**

Port Security: Ograniczenie portów dostępowych do 1 adresu MAC

ACL: filtrowanie ruchu między VLANami

SSH: Wyłączenie Telnetu, generowanie kluczy RSA, konfiguracja dostępu do vty

<br>

**Struktura repozytorium:**

```text
ccna-eve-ng-lab/
│
├── README.md
│
├── topology/
│   ├── diagram.png               # Schemat graficzny topologii
│   └── topology.unl              # Plik eksportu topologii z EVE-NG
│
├── configs/
│   ├── R1_startup.cfg            # Konfiguracja routera brzegowego R1
│   ├── R2_startup.cfg            # Konfiguracja routera dystrybucyjnego R2
│   ├── SW1_startup.cfg           # Konfiguracja przełącznika SW1
│   ├── SW2_startup.cfg           # Konfiguracja przełącznika SW2
│   └── SW3_startup.cfg           # Konfiguracja przełącznika SW3
│
├── docs/
│   ├── 01_addressing_table.md    # Tabela adresacji IP / VLSM / WAN
│   ├── 02_vlan_table.md          # Tabela VLAN-ow, trunki i subinterfejsy
│   ├── 03_stp_analysis.md        # Analiza STP/RSTP, role portow, split root
│   ├── 04_ospf_routing.md        # Konfiguracja i weryfikacja OSPFv2, Zero-Trust
│   ├── 05_network_services.md    # DHCP, NAT/PAT overload, SVI, NTP
│   ├── 06_network_security.md    # Port Security, Extended ACL, SSHv2, VTY hardening
│   └── 07_troubleshooting_log.md # Dziennik awarii i analizy przyczyn (RCA)
│
└── screenshots/
    ├── topology_eve.png          # Zrzut ekranu z EVE-NG
    ├── stp_verification.png      # Weryfikacja stanow portow STP
    └── ping_tests.png            # Testy lacznosci End-to-End

```

**Autor**

**Jakub Holak | Technik IT | Student Informatyki, Politechnika Śląska | https://www.linkedin.com/in/holakjakub/**

	"Aby dokumentacja była dobra trzeba w nią włożyć serce"

