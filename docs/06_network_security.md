# Bezpieczeństwo Sieci

Zakres: Port Security, Extended ACL oraz bezpieczny dostęp SSHv2

---

## 1. Port Security na switchach

Porty dostępowe dla stacji roboczych (np. Gi0/2) zabezpieczono mechanizmem Port Security:
- Maksymalna liczba dozwolonych adresów MAC: 1
- Tryb uczenia: sticky (adres jest automatycznie pobierany z pierwszego pakietu i dodawany do konfiguracji)
- Reakcja na naruszenie: violation shutdown (natychmiastowe przejście w stan err-disable)

Wynik testu naruszenia:  
Podłączenie stacji o nieautoryzowanym adresie MAC wywołało natychmiastowe zablokowanie portu:
```text
%PM-4-ERR_DISABLE: psecure-violation error detected on Gi0/2, putting Gi0/2 in err-disable state
```

Procedura odzyskiwania wymaga przejścia przez wyłączenie i włączenie interfejsu (shutdown / no shutdown).  
Ważne: nauczone adresy sticky należy utrwalić w pamięci trwałej poleceniem zapisu, aby po restarcie switcha nie zostały potraktowane jako obce.

---

## 2. Extended ACL na routerze

Na subinterfejsie Gi0/1.10 wdrożono rozszerzoną listę kontroli dostępu `BLOCK_USERS_TO_SERVICES` w kierunku wejściowym (in):
- Blokada ruchu z podsieci użytkowników 172.16.0.0/24 do serwerów produkcyjnych 172.16.1.64/27
- Zezwolenie na pozostały ruch (wyjście do Internetu oraz komunikacja z działem IT)

Wynik weryfikacji:  
Próba wysłania pakietu ICMP z hosta użytkowników do serwera kończy się odrzuceniem i odesłaniem komunikatu:
```text
ICMP type:3, code:13 - Communication administratively prohibited
```

---

## 3. SSHv2 i ochrona linii VTY

Dostęp administracyjny zabezpieczono na poziomie warstwy zarządzania:
- Wyłączono nieszyfrowany protokół Telnet na rzecz SSHv2 z asymetrycznym kluczem RSA 2048 bitów.
- Dostęp do linii VTY ograniczono listą kontroli dostępu wyłącznie dla podsieci zarządzania (VLAN 99). Próby połączenia z podsieci użytkowników są odrzucane.
- Utworzono lokalne konto administratora z najwyższym poziomem uprawnień (privilege 15 secret).

Z powodu braku modułu kryptograficznego w obrazie vIOS na R1, pełny stos SSHv2 wdrożono na switchach vIOS-L2.

Pełne pliki konfiguracyjne znajdują się w katalogu `configs/`.
