# EIGRP  

### Цель:  
- Настроить EIGRP в С.-Петербург;
- Использовать named EIGRP

- В офисе С.-Петербург настроить EIGRP  
- R32 получает только маршрут по умолчанию  
- R16-17 анонсируют только суммарные префиксы  
- Использовать EIGRP named-mode для настройки сети  
- Настройка осуществляется одновременно для IPv4 и IPv6  

Topology IPv4:  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/EIGRP/EIGRP_IPv4.png)  

Topology IPv6:  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/EIGRP/SPB-IPv6-Topology.png)  

## Настройка:  
Настроим EIGRP на R16-R17, поднимем процесс EIGRP SPB (AS 40), анонсируем сети, суммируем маршруты на уровне интерфейсов, установим пассивные интерфейсы.   
#### Для R16 IPv4:  
!  
router eigrp SPB  
 address-family ipv4 unicast autonomous-system 40  
  network 10.210.50.4 0.0.0.3  
  network 10.210.50.8 0.0.0.3  
  network 172.27.30.0 0.0.0.255  
  network 172.27.40.0 0.0.0.255  
  eigrp router-id 5.5.5.16  
  !  
  af-interface Ethernet0/0  
   passive-interface  
  af-interface Ethernet0/1  
   summary-address 10.210.50.0 255.255.255.0  
   summary-address 172.27.0.0 255.255.0.0  
  af-interface Ethernet0/2  
   passive-interface  
  af-interface Ethernet0/3  
   summary-address 0.0.0.0 0.0.0.0  
!  
#### Для R16 IPv6:  
address-family ipv6 unicast autonomous-system 40  
!  
eigrp router-id 5.5.5.16  
!  
 af-interface Ethernet0/1  
   summary-address 2024:ABAD:1981::/48  
  af-interface Ethernet0/3  
   summary-address ::/0  
  af-interface Ethernet0/0  
   passive-interface  
  af-interface Ethernet0/2  
   passive-interface  
!  
#### Для R17 IPv4:  
!  
router eigrp SPB  
 address-family ipv4 unicast autonomous-system 40  
  network 10.210.50.0 0.0.0.3  
  network 172.27.30.0 0.0.0.255  
  network 172.27.40.0 0.0.0.255  
  eigrp router-id 5.5.5.17  
  !  
  af-interface Ethernet0/0  
   passive-interface  
  af-interface Ethernet0/1  
   summary-address 10.210.50.0 255.255.255.0  
   summary-address 172.27.0.0 255.255.0.0  
  af-interface Ethernet0/2  
   passive-interface   
!  
#### Для R17 IPv6:  
 address-family ipv6 unicast autonomous-system 40  
  eigrp router-id 5.5.5.17  
!  
  af-interface Ethernet0/0  
   passive-interface  
  af-interface Ethernet0/1  
   summary-address 2024:ABAD:1981::/48  
  af-interface Ethernet0/2  
   passive-interface    
!  

#### Для R18 IPv4:  
router eigrp SPB  
 address-family ipv4 unicast autonomous-system 40  
  !  
  network 10.210.50.0 0.0.0.3  
  network 10.210.50.4 0.0.0.3  
  eigrp router-id 5.5.5.18  
#### Для R18 IPv6:  
 address-family ipv6 unicast autonomous-system 40  
  !  
  eigrp router-id 5.5.5.18  

#### Для R32 IPv4:  
router eigrp SPB  
 address-family ipv4 unicast autonomous-system 40  
  !  
  network 10.210.50.8 0.0.0.3  
  eigrp router-id 5.5.5.32  
#### Для R32 IPv6:  
 address-family ipv6 unicast autonomous-system 40  
  !  
  eigrp router-id 5.5.5.32  

## Проверка 
Согласно таблице маршрутизации R32 получает только маршрут по умолчанию   
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/EIGRP/IP-Route-R32.png)  

R16  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/EIGRP/IP-Route-R16.png)  
R17  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/EIGRP/IP-Route-R17.png)  