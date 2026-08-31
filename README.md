CCNA HOME Lab - EVE-NG Network Simulation

Środowisko laboratoryjne symulujące sieć korporacyjną, zbudowane w EVE-NG z wykorzystaniem obrazów Cisco vIOS. Projekt realizuje pełen zakres tematyczny egzaminu CISCO CCNA 200-301.

Spis treści:
O projekcie
Topologia
Technologie
Fazy projektu
Struktura repozytorium

O projekcie:
Projekt ma dwa cele:
1. Edukacyjny - praktyczne wdrożenie każdego modułu egzaminu CCNA 200-301 w środowisku laboratoryjnym.
2. Portfolio - zademonstrowanie umiejętności projektowania, konfiguracji i zabezpieczania sieci korporacyjnej - enterprise.

Sieć odzwierciedla typową architekturę średniej firmy:
strefa użytkowników,
serwerów,
segment zarządzania,
IT,
połączone z internetem przez NAT.

Topologia
```
					     Internet
						|
			   Gi0/0-----------------------------------Gi0/0
                 R1  				                     R2
               Gi0/1  				                   Gi0/1          
                  |				                         |
			   Gi0/0 				                   Gi0/0	              
    ------Gi0/2 SW1				                        SW2 Gi0/2------
    |	   Gi0/1		     		                   Gi0/1          |
   VPC6	     |	                                         |           VPC8
             -------------Gi0/0 SW3 Gi0/1-----------------
					          Gi0/2
                                 |
						        VPC7
```	
Trójkąt STP: SW1->SW3<-SW2
(redundancja ścieżki przez SW3 wymusza STP)

Urządzenia (nazwa - model - rola):
R1 - Cisco vIOS - Router brzegowy, DHCP, NAT, NTP master
R2 - Cisco vIOS - Router dystrubcji, OSPF
SW1 - Cisco vIOS-L2 - Root Bridge (STP), EtherChannel
SW2 - Cisco vIOS-L2 - Dystrybucja, EtherChannel
SW3 - Cisco vIOS-L2 - Dostęp (Access layer)
VPCx3 - EVE-NG VPC - Hosty testowe

Technologie:

Kategoria		Stack
Hiper nadzorca   	VMwaare (NAT adapter)
Emulator sieci	 	EVE-NG Community
Obrazy			Cisco vIOS (QEMU), Cisco vIOS-L2 (QEMU)
Transfer plików		SFTP (przez WinSCP)
Konsola CLI 		PuTTY

Fazy projektu:

(Zakończone)
Faza 1 - Środowisko i warstwa fizyczna
Przygotowanie infrastruktury:
Konfiguracja NAT
Transfer obrazów Cisco vIOS przez SFTP (zadbanie o prawidłową strukturę katalogów vios-, viosl2-)
Naprawa uprawnień (fixpermissions)
Wdrożenie topologii
Integracja konsoli PuTTY

(W trakcie)
Faza 2 - Fundamenty i adresacja IP
Projektowanie schematu adresacji metodą VLSM:

Sieć / Zastosowanie	Adres sieci	Maska	Zakres użytkowy	Ilość hostów
VLAN 10 – Użytkownicy	172.16.0.0	/24	0.1 – 0.254	254
VLAN 20 – IT	        172.16.1.0	/26	1.1 – 1.62	62
VLAN 30 – Serwery	172.16.1.64	/27	1.65 – 1.94	30
VLAN 99 – Zarządzanie	172.16.1.96	/28	1.97 – 1.110	14
Link P2P R1<->R2	172.16.1.112	/30	1.113 – 1.114	2

Wdrożone podstawowe "Quality of Life":
logging synchronous
no ip domain-lookup
hostname ………………… (w zależności od urządzenia)

(Planowane)
Faza 3 - Switching (moduł B CCNA)
VLANy: VLAN 10,20,30,99 na przełącznikach
Access/Trunk: Porty dostępowe dla VPC, łącza 802.1Q trunk między urządzeniami L2/L3
STP/RSTP: Analiza topologii, ręczna manipulacja priorytetami -> SW1 jako Root Bridge, migracja do STP
Ether Channel (LACP): Agregacja dwóch łączy między SW1 a SW2 dla redundancji i przepustowości

Faza 4 - Routing (moduł C CCNA)
Routing między VLANami (Router-on-a-stick): subinterfejsy Gi0/1.10, Gi0/1.20, Gi0/1.30
Routing statyczny: Default Route na routerze brzegowym
OSPFv2 (single-area): Automatyczna wymiana tras między R1 i R2

Faza 5 - Usługi sieciowe (moduł D CCNA)
DHCP: Serwer DHCP na routerze, automatyczne przydzielanie adresów
NAT/PAT: Translacja 172.16.0.0/22 na jeden publiczny IP na R1
NTP: Synchronizacja czasu między urządzeniami (pod analizę logów)

Faza 6 - Bezpieczeństwo
Port Security: Ograniczenie portów dostępowych do 1 adresu MAC
ACL: filtrowanie ruchu między VLANami
SSH: Wyłączenie Telnetu, generowanie kluczy RSA, konfiguracja dostępu do vty

Struktura repozytorium (W BUDOWIE)

Autor
Jakub Holak | Technik IT | Student Informatyki, Politechnika Śląska | https://www.linkedin.com/in/holakjakub/

	"Aby dokumentacja była dobra trzeba w nią włożyć serce"













































					
