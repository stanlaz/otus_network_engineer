# BGP. Основы  

#### Цель:  
### астроить BGP между автономными системами  
Организовать доступность между офисами Москва и С.-Петербург  

- Настроить eBGP между офисом Москва и двумя провайдерами (Киторн и Ламас)  
- Настроить eBGP между провайдерами Киторн и Ламас  
- Настроить eBGP между Ламас и Триада  
- Настроить eBGP между офисом С.-Петербург и провайдером Триада  
- Организовать IP доступность между пограничным роутерами (офисами Москва и С.-Петербург)  

#### Topology:  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/BGP/eBGP-Topology.png)  

Конфигурация:  
### Часть 1:  Настроим eBGP на R14-R15 (AS 1001): анонсируем сети, укажем соседей и номер автономной системы для активации сессии BGP.  
R14:  
!  
router bgp 1001  
 bgp router-id 4.4.4.14  
 bgp log-neighbor-changes  
 network 4.4.4.14 mask 255.255.255.255  
 neighbor 75.100.50.22 remote-as 101  
!  
R15:  
router bgp 1001
 bgp router-id 4.4.4.15
 bgp log-neighbor-changes
 network 4.4.4.15 mask 255.255.255.255
 neighbor 90.100.50.9 remote-as 301

### Часть 2-3:  Настроим eBGP на R21(AS 301)-R22(AS 101): анонсируем сети, укажем соседей и номер автономной системы для активации сессии BGP.  
R21:  
router bgp 301  
 bgp router-id 2.2.2.21  
 bgp log-neighbor-changes  
 network 2.2.2.21 mask 255.255.255.255  
 neighbor 75.100.50.9 remote-as 101  
 neighbor 80.80.50.21 remote-as 520  
 neighbor 90.100.50.10 remote-as 1001  
!  
R22:  
router bgp 101  
 bgp router-id 3.3.3.22  
 bgp log-neighbor-changes  
 network 3.3.3.22 mask 255.255.255.255  
 neighbor 75.100.50.10 remote-as 301  
 neighbor 75.100.50.21 remote-as 1001  
!  
### Часть 3:  Настроим eBGP на R24(AS 520): анонсируем сети, укажем соседей и номер автономной системы для активации сессии BGP.  
R24:  
!  
router bgp 520  
 bgp router-id 1.1.1.24  
 bgp log-neighbor-changes  
 network 1.1.1.24 mask 255.255.255.255  
 neighbor 80.80.50.22 remote-as 301  
 neighbor 80.80.50.82 remote-as 2042  
 !  
 ### Часть 4:  Настроим eBGP на R18(AS 2042): анонсируем сети, укажем соседей и номер автономной системы для активации сессии BGP.  
!  
router bgp 2042  
 bgp router-id 5.5.5.18  
 bgp log-neighbor-changes  
 network 5.5.5.18 mask 255.255.255.255  
 neighbor 80.80.50.81 remote-as 520  
### Часть 5:  Проверим доступность между роутерами R14 и R18:  

таблица маршрутизации R18:  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/BGP/IP-Route-R18.png)  

таблица маршрутизации R14:  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/BGP/IP-Route-R14.png)  

Проверка доступности R18 с R14:  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/BGP/Echo-R14-R18.png)  