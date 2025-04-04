# Основные протоколы сети интернет  

### План работ:  
#### Настроить NAT(PAT) на R14 и R15. Трансляция должна осуществляться в адрес автономной системы AS1001.  
#### Настроить NAT(PAT) на R18. Трансляция должна осуществляться в пул из 5 адресов автономной системы AS2042.  
#### Настроить статический NAT для R20.  
#### Настроить NAT так, чтобы R19 был доступен с любого узла для удаленного управления.  
#### Настроить статический NAT(PAT) для офиса Чокурдах.  
#### Настроить для IPv4 DHCP сервер в офисе Москва на маршрутизаторах R12 и R13. VPC1 и VPC7 должны получать сетевые настройки по DHCP.  
#### Настроить NTP сервер на R12 и R13. Все устройства в офисе Москва должны синхронизировать время с R12 и R13.  

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
### Часть1: Настроим NAT (PAT) для R18  
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

