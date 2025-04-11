# VPN. GRE. DmVPN  
### Цель:  
- ## Настроить GRE между офисами Москва и С.-Петербург.  
- ## Настроить DMVMN между Москва и Чокурдах, Лабытнанги.  
### Часть 1: настроим GRE между R15 и R18  
R15:  
!  
interface Tunnel1  
 description ---==GRE-MSK-SPB==---  
 ip address 10.50.20.9 255.255.255.252  
 ip mtu 1400  
 ip tcp adjust-mss 1360  
 tunnel source 90.100.50.10  
 tunnel destination 80.80.50.82  
!  
Анонсируем статические маршруты до сетей филиалов:  
ip route 6.6.6.27 255.255.255.255 10.50.30.12 (Чокурдах)  
ip route 5.5.5.32 255.255.255.255 10.50.20.10 (С.-Петербург)  
ip route 172.27.30.0 255.255.255.0 Tunnel1 (С.-Петербург)  
ip route 172.27.40.0 255.255.255.0 Tunnel1 (С.-Петербург)  
ip route 172.27.50.0 255.255.255.0 10.50.30.13 (Лабытнаги)  
!  

R18:  
!  
interface Tunnel1  
 description ---==GRE-MSK-SPB==---  
 ip address 10.50.20.10 255.255.255.252  
 ip mtu 1400  
 ip tcp adjust-mss 1360  
 tunnel source 80.80.50.82  
 tunnel destination 90.100.50.10  
!  
Анонсируем статические маршруты до сетей филиала Москва:
ip route 172.27.10.0 255.255.255.0 Tunnel1  
ip route 172.27.20.0 255.255.255.0 Tunnel1  
!  
### Часть 2: настроим DMVPN между R15, R27, R28:  
  Для настройки используем топологию Hub-and-Spoke, в качестве центрального HUB установим R15:   

R15:  
!  
interface Tunnel2  
 description ---==DMVPN-MSK-LAB-CHOCK==---  
 ip address 10.50.30.11 255.255.255.248  
 no ip redirects  
 ip mtu 1400  
 ip nhrp authentication filials  
 ip nhrp map multicast dynamic  
 ip nhrp network-id 2  
 ip tcp adjust-mss 1360  
 load-interval 30  
 keepalive 5 10  
 tunnel source Ethernet0/2  
 tunnel mode gre multipoint  
!  
R27:  
interface Tunnel2  
 description ---==DMVPN-MSK-LAB-CHOCK==---  
 ip address 10.50.30.12 255.255.255.248  
 no ip redirects  
 ip mtu 1400  
 ip nhrp authentication filials  
 ip nhrp map multicast dynamic  
 ip nhrp map 10.50.30.11 90.100.50.10  
 ip nhrp map multicast 90.100.50.10  
 ip nhrp network-id 2  
 ip nhrp nhs 10.50.30.11  
 ip nhrp registration no-unique  
 ip tcp adjust-mss 1360  
 load-interval 30  
 keepalive 5 10  
 tunnel source Ethernet0/0  
 tunnel mode gre multipoint  
 !  
 Анонсируем статические маршруты до сетей филиала Москвва:  
ip route 172.27.10.0 255.255.255.0 10.50.30.11  
ip route 172.27.20.0 255.255.255.0 10.50.30.11 
!   
R28:  
!  
interface Tunnel2  
 description ---==DMVPN-MSK-LAB-CHOCK==---  
 ip address 10.50.30.13 255.255.255.248  
 no ip redirects  
 ip mtu 1400  
 ip nhrp authentication filials  
 ip nhrp map multicast dynamic  
 ip nhrp map 10.50.30.11 90.100.50.10  
 ip nhrp map multicast 90.100.50.10  
 ip nhrp network-id 2  
 ip nhrp nhs 10.50.30.11  
 ip nhrp registration no-unique  
 ip tcp adjust-mss 1360  
 load-interval 30  
 keepalive 5 10  
 tunnel source Ethernet0/0  
 tunnel mode gre multipoint  
!  
Анонсируем статические маршруты до сетей филиала Москвва:  
ip route 172.27.10.0 255.255.255.0 10.50.30.11  
ip route 172.27.20.0 255.255.255.0 10.50.30.11  

### Часть 3: проверка:  



