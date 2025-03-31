# BGP Фильтрация  
## Цель:  
#### Настроить фильтрацию в офисе Москва так, чтобы не появилось транзитного трафика(As-path).  
#### Настроить фильтрацию в офисе С.-Петербург так, чтобы не появилось транзитного трафика(Prefix-list).  
#### Настроить провайдера Киторн так, чтобы в офис Москва отдавался только маршрут по умолчанию.  
#### Настроить провайдера Ламас так, чтобы в офис Москва отдавался только маршрут по умолчанию и префикс офиса С.-Петербург.  
#### Все сети в лабораторной работе должны иметь IP связность.  
#### План работы и изменения зафиксированы в документации.  

### Topology:  

![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/BGP%20FILTER/BGP-Topology.png)  

### Часть1: Настроим фильтрацию в офисе Москва на основании атрибута AS PATH  
Чтобы избежать транзита трафика, cоздадим специальный тип access-list:  
для R14:  
!  
ip as-path access-list 4 permit ^$  
router bgp 1001  
neighbor 75.100.50.22 filter-list 4 out  
!  

для R15:  
!  
ip as-path access-list 4 permit ^$  
router bgp 1001  
neighbor 90.100.50.9 filter-list 4 out  
! 
Таблица соседей BGP R21  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/BGP%20FILTER/IP-BGP-R21.png)  
Таблица соседей BGP R22  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/BGP%20FILTER/IP-BGP-R22.png)  

### Часть2:  Настроим фильтрацию в офисе С.-Петербург  
Создадим списки префиксов (prefix-list) для сетей C.-Петербург и применим наш фильтр:  
R18:  
!  
ip prefix-list NO-TRAN seq 5 permit 5.5.5.18/32  
ip prefix-list NO-TRAN seq 6 permit 5.5.5.17/32  
ip prefix-list NO-TRAN seq 7 permit 5.5.5.16/32  
ip prefix-list NO-TRAN seq 8 permit 5.5.5.32/32  
ip prefix-list NO-TRAN seq 10 permit 10.210.50.0/30  
ip prefix-list NO-TRAN seq 15 permit 10.210.50.4/30  
ip prefix-list NO-TRAN seq 20 permit 10.210.50.8/30  
ip prefix-list NO-TRAN seq 25 permit 172.27.30.0/24  
ip prefix-list NO-TRAN seq 30 permit 172.27.40.0/24  
ip prefix-list NO-TRAN seq 35 deny 0.0.0.0/0 le 32  
!  
router bgp 2042  
 neighbor 80.80.50.73 remote-as 520  
 neighbor 80.80.50.73 prefix-list NO-TRAN out  
 neighbor 80.80.50.81 remote-as 520  
 neighbor 80.80.50.81 prefix-list NO-TRAN out  
!  

### Часть3:  Настройм провайдера Киторн так, чтобы в офис Москва отдавался только маршрут по умолчанию
Для R22 создадим route-map и настроим маршрут по умолчанию в сторону R14:  
R22:  
!    
route-map DEFAULT deny 10  
router bgp 101  
 neighbor 75.100.50.21 default-originate  
 neighbor 75.100.50.21 route-map DEFAULT out  
!
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/BGP%20FILTER/IP-BGP-R14.png)  

### Часть4:  Настроим провайдера Ламас так, чтобы в офис Москва отдавался только маршрут по умолчанию и префикс офиса С.-Петербург  
Настроим маршрут по умолчанию для R15 и список доступа к AS2042:  
!  
ip as-path access-list 1 permit _2042$  
router bgp 301  
neighbor 90.100.50.10 default-originate  
 neighbor 90.100.50.10 filter-list 1 out  
!  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/BGP%20FILTER/IP-BGP-R15.png)  

### Часть5:  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/BGP%20FILTER/ECHO-VPC1-OTHER.png)  