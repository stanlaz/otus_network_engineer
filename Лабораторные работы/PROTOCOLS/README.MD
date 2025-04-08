# Основные протоколы сети интернет  

### План работ:  
- #### Настроить NAT(PAT) на R14 и R15. Трансляция должна осуществляться в адрес автономной системы AS1001.  
- #### Настроить NAT(PAT) на R18. Трансляция должна осуществляться в пул из 5 адресов автономной системы AS2042.  
- #### Настроить статический NAT для R20.  
- #### Настроить NAT так, чтобы R19 был доступен с любого узла для удаленного управления.  
- #### Настроить статический NAT(PAT) для офиса Чокурдах.  
- #### Настроить для IPv4 DHCP сервер в офисе Москва на маршрутизаторах R12 и R13. VPC1 и VPC7 должны получать сетевые настройки по DHCP.  
- #### Настроить NTP сервер на R12 и R13. Все устройства в офисе Москва должны синхронизировать время с R12 и R13.  

### Часть1: Настроим NAT для R14-R15 (AS1001)  

Для R14:  
!  
access-list 10 permit 172.27.10.0 0.0.0.255  
access-list 10 permit 172.27.20.0 0.0.0.255  
!  
ip nat inside source list 10 interface Ethernet0/2 overload  
!  
interface Ethernet0/0  
  ip nat inside  
interface Ethernet0/1  
  ip nat inside  
interface Ethernet0/2  
 ip nat outside  
interface Ethernet0/3  
  ip nat inside  
!  
Для R15:  
!  
access-list 10 permit 172.27.10.0 0.0.0.255  
access-list 10 permit 172.27.20.0 0.0.0.255  
!  
ip nat inside source list 10 interface Ethernet0/2 overload  
!  
interface Ethernet0/0  
  ip nat inside  
interface Ethernet0/1  
  ip nat inside  
interface Ethernet0/2  
 ip nat outside  
interface Ethernet0/3  
  ip nat inside  
!  

Проверка:  
!
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/PROTOCOLS/nat-translations.png)  

### Часть2: Настроим NAT (PAT) для R18  
!  
interface Ethernet0/0  
 ip nat inside  
interface Ethernet0/1  
 ip nat inside  
interface Ethernet0/2  
 ip nat outside  
interface Ethernet0/3  
 ip nat outside  
!  
access-list 15 permit 172.27.30.0 0.0.0.255  
access-list 15 permit 172.27.40.0 0.0.0.255  
route-map SPB-R24 permit 10  
 match ip address 15  
 match interface Ethernet0/2  
!  
route-map SPB-R26 permit 10  
 match ip address 15  
 match interface Ethernet0/3  
!  
ip nat pool SPB-R26 80.80.50.100 80.80.50.104 prefix-length 28  
ip nat pool SPB-R24 80.80.50.85 80.80.50.89 prefix-length 28  
ip nat inside source route-map SPB-R24 pool SPB-R24  
ip nat inside source route-map SPB-R26 pool SPB-R26  
!  
Проверка:  
!
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/PROTOCOLS/nat-translations-R18.png)  

### Часть3: Настроим статический NAT для R20:  
для R15:  
!  
ip nat inside source static 10.200.50.22 90.100.50.10  
!  
Проверка:  
!
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/PROTOCOLS/nat-translations-R20.png)  
!
### Часть4: Настроим NAT так, чтобы R19 был доступен с любого узла для удаленного управления:  
!  
ip nat inside source static 10.200.50.2 75.100.50.21  
!  
### Часть5: Настроим NAT(PAT) для офиса Чокурдах.  
!  
interface Ethernet0/0  
 ip nat outside    
interface Ethernet0/2  
 ip nat inside  
!  
access-list 20 permit 172.27.50.0 0.0.0.255  
ip nat inside source list 20 interface Ethernet0/0 overload  
!  
### Часть6: Настроим DHCP сервер в офисе Москва.  
Для R12:  
!  
ip dhcp excluded-address 172.27.10.1  
ip dhcp excluded-address 172.27.10.2  
ip dhcp pool VPC1  
 network 172.27.10.0 255.255.255.0  
 default-router 172.27.10.1  
 domain-name MOSCOW  
!  
Для R13:  
!  
ip dhcp excluded-address 172.27.20.1  
ip dhcp excluded-address 172.27.20.2  
ip dhcp pool VPC7  
 network 172.27.20.0 255.255.255.0  
 default-router 172.27.20.1  
 domain-name MOSCOW  
!  
#### Проверка:  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/PROTOCOLS/DHCP-VPC.png)  

### Часть7: Настроим NTP сервер на R12 и R13:  
!  
interface Ethernet0/0  
 ntp broadcast  
interface Ethernet0/1  
 ntp broadcast  
interface Ethernet0/2  
 ntp broadcast  
interface Ethernet0/3  
 ntp broadcast  
!  
ntp master 1  
ntp update-calendar  
!  
На клиентах:  
ntp server 4.4.4.12  
ntp server 4.4.4.13  
!  
Проверка:  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/PROTOCOLS/NTP-R14.png)  


