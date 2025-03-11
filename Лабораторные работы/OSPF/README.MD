# OSPF  
## настроить OSPF в офисе Москва  
## Разделить сеть на зоны  
## Настроить фильтрацию между зонами  
- Маршрутизаторы R14-R15 находятся в зоне 0 - backbone  
- Маршрутизаторы R12-R13 находятся в зоне 10. Дополнительно к маршрутам должны получать маршрут по умолчанию  
- Маршрутизатор R19 находится в зоне 101 и получает только маршрут по умолчанию  
- Маршрутизатор R20 находится в зоне 102 и получает все маршруты, кроме маршрутов до сетей зоны 101  
- Настройка для IPv6 повторяет логику IPv4  

topology:  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/OSPF/Topology.png)  

### Часть 1: настроим маршрутизаторы в зоне 0 (R14 и R15), поднимем процесс OSPF, анонсируем необходимые сети:  
#### R14:  
!  
router ospf 50  
router-id 4.4.4.14  
area 101 stub no-summary  
network 4.4.4.14 0.0.0.0 area 0  
network 10.200.50.0 0.0.0.3 area 101  
network 10.200.50.4 0.0.0.3 area 0  
network 10.200.50.8 0.0.0.3 area 0  
network 75.100.50.20 0.0.0.3 area 0  
default-information originate  
!  
Интерфейс e 0/3 относится к зоне 101  
#### R15:  
!  
router ospf 50  
router-id 4.4.4.15  
network 4.4.4.15 0.0.0.0 area 0  
network 10.200.50.12 0.0.0.3 area 0  
network 10.200.50.16 0.0.0.3 area 0  
network 10.200.50.20 0.0.0.3 area 102  
network 90.100.50.8 0.0.0.3 area 0  
default-information originate  
!  
Интерфейс e 0/3 относится к зоне 102  

### Часть 2: настроим маршрутизаторы в зоне 10 (R12 и R13), поднимем процесс OSPF, анонсируем необходимые сети:  

#### R12:  
!  
router ospf 50  
router-id 4.4.4.12  
network 4.4.4.12 0.0.0.0 area 0  
network 10.200.50.4 0.0.0.3 area 0  
network 10.200.50.12 0.0.0.3 area 0  
network 172.27.10.0 0.0.0.255 area 10  
network 172.27.20.0 0.0.0.255 area 10  
!  
#### R13:  
!  
router ospf 50  
router-id 4.4.4.13  
network 4.4.4.13 0.0.0.0 area 0  
network 10.200.50.8 0.0.0.3 area 0  
network 10.200.50.16 0.0.0.3 area 0  
network 172.27.10.0 0.0.0.255 area 10  
network 172.27.20.0 0.0.0.255 area 10  
!  
Интерфейсы e 0/0, e 0/1 относятся к зоне 0; интерфейсы e 0/2, e 0/3 - к зоне 10  

### Часть 3: настроим маршрутизаторы в зоне 101 (R19), поднимем процесс OSPF, анонсируем необходимые сети, кроме того зона 101 является totally stubby:  
#### R19:  
!  
router ospf 50  
router-id 4.4.4.19  
area 101 stub no-summary  
network 4.4.4.19 0.0.0.0 area 101  
network 10.200.50.0 0.0.0.3 area 101  
!  
### Часть 4: настроим маршрутизаторы в зоне 102 (R20), поднимем процесс OSPF, анонсируем необходимые сети, так же R20 не должен получать маршрутов до 101 зоны (применим фильтрацию маршрутов при помощи preffix-list на R15):  
#### R20:  
!  
router ospf 50  
router-id 4.4.4.20  
network 4.4.4.20 0.0.0.0 area 102  
network 10.200.50.20 0.0.0.3 area 102  
!  
#### R15:  
!  
ip prefix-list AREA-101 seq 5 deny 4.4.4.19/32  
ip prefix-list AREA-101 seq 10 deny 10.200.50.0/30  
ip prefix-list AREA-101 seq 15 permit 0.0.0.0/0 le 32  
!  
area 102 filter-list prefix AREA-101 in  
!  
Дополнительно значение приоритетов интерфейсов e0/0 R19 и e0/0 R20 в OSPF были установлены равными 0, для того чтобы данные маршрутизаторы не участвовали в выборах DR  
### Часть 5: проверим условия задания:  
#### Таблица маршрутизации R19:  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/OSPF/R19_routes.png)  
В таблице маршрутизации присутствует только маршрут по умолчанию  

#### Таблица маршрутизации R20:  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/OSPF/R20_routes.png)  
В таблице маршрутизации маршруты до сетей R19 (10.200.50.0/30 и 4.4.4.19/32) отсутствуют    

### Часть 6: Настройка IPV6:  
topology  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/OSPF/OSPF_IPv6.png)  

#### логика процесса настройки схожа с IPv4:  
- Включаем процесс маршрутизации на R12-R15, R19-R20, поднимаем процесс OSPFv3, назначаем router-id для каждого маршрутизатора.  
 ipv6 unicast-routing  
 ipv6 router ospf 50  
- далее на каждом интерфейсе также задействуем процесс OSPFv3 и объявляем зоны  
 ipv6 enable  
 ipv6 ospf 50 area 0 (пример для интерфейсов в зоне 0)  
#### дополнительно:  
- для маршрутизаторов R14-R15 настроим трансляцию маршрутов по умолчанию и маршруты до сетей провайдера:  
 default-information originate  
 ipv6 route ::/0 2024:BACD:2020:1::1 (для R14)  
- для R19 настроим totally stubby area (на интерфейсах)  
ipv6 ospf 50 area 101  
- для R20 настроим фильтрацию маршрутов до R19 (настраиваем префикс-листы на R15)  
 ipv6 prefix-list AREA-101 seq 5 deny 2024:BACD:2020:9::/64  
 ipv6 prefix-list AREA-101 seq 10 permit ::/0 le 128  
 ipv6 router ospf 50  
 area 102 filter-list prefix AREA-101 in  
- проверяем условия задания:  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/OSPF/IPv6_R19.png)  
В таблице маршрутизации присутствует только маршрут по умолчанию  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/OSPF/IPv6_R20.png)  
В таблице маршрутизации маршруты до R19  отсутствуют  