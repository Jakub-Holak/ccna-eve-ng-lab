# Dziennik Rozwiązywania Problemów

Zestawienie błędów, na które natknąłem się podczas konfiguracji laba, co było ich przyczyną i jak je rozwiązałem.

| Lp. | Co się działo (objaw) | Co było nie tak | Jak to naprawiłem |
|---|---|---|---|
| 1 | Brak pingu między R1 a R2 na łączu P2P | Przez pomyłkę stworzyłem subinterfejsy na porcie Gi0/0 zamiast Gi0/1 na R1. | Usunąłem subinterfejsy z Gi0/0, ustawiłem tam zwykły adres IP /30 i przeniosłem subinterfejsy na właściwy port Gi0/1. |
| 2 | Co drugi ping do Internetu miał timeout (50% strat) | Na R1 miałem wbite dwie trasy domyślne: jedną dobrą na adres operatora (.1), a drugą złą na adres sieci (.0). Router wysyłał pakiety na zmianę. | Wpisałem `show ip route 0.0.0.0`, zauważyłem podwójną trasę i usunąłem tę błędną komendą `no ip route 0.0.0.0 0.0.0.0 203.0.113.0`. |
| 3 | OSPF nie rozgłaszał podsieci VLAN 10 | Zwykła literówka: wbiłem `network 176.16.0.0` zamiast `172.16.0.0`. | Sprawdziłem konfigurację w `show ip protocols`, usunąłem zły wpis i dodałem poprawny adres sieci. |
| 4 | W konsoli co chwilę wyskakiwał błąd o Native VLAN mismatch | Na porcie między SW1 a SW3 zapomniałem dopisać `switchport trunk native vlan 99` i switch nadal używał domyślnego VLAN 1. | Wszedłem na port na switchu i dopisałem brakującą komendę z Native VLAN 99. |
| 5 | Ping przechodził przez router mimo reguły ACL | W regule ACL podałem zwykłą maskę 255.255.255.0 zamiast maski wildcard 0.0.0.255. | Sprawdziłem listę przez `show access-lists`, poprawiłem regułę wpisując właściwy wildcard. |
| 6 | Port wchodził w stan err-disable po restarcie switcha | Switch nauczył się adresu MAC przez `sticky`, ale nie zapisałem konfiguracji do pamięci. Po restarcie uznał ten sam komputer za intruza. | Po nauczeniu adresów wykonałem `write memory` (`wr`), żeby konfiguracja została na stałe. |
| 7 | Brak komendy `crypto key generate` na routerze R1 | Użyty obraz vIOS dla routera nie miał wsparcia dla kryptografii. | Zamiast na routerze, pełne SSH skonfigurowałem na switchach L2, które obsługiwały moduł crypto. |
