# Часть 1 создадим сеть и настроим базовые параметры устройств
1. запишем таблицу адресации и рассчитаем подсети согласно заданию
#### Addressing Table
Device    |	Interface    | IP Address     | Subnet Mask    | Default Gateway|  
------    | ------------ | -------------- | -------------- | ---------------|
R1	      | e0/0         |	10.0.0.1      | 255.255.255.252|      N/A       |
R1        | e0/1         |	N/A           |	N/A            |                |
R1        | e0/1.100     | 192.168.1.1    | 255.255.255.192|                |
R1        | e0/1.200     | 192.168.1.65   | 255.255.255.224|                |
R1        | e0/1.1000    | N/A            |	N/A            |                |
R2	      | e0/0         | 10.0.0.2       | 255.255.255.252|      N/A       |
R2        | e0/1         | 192.168.1.97   | 255.255.255.240|                |
S1        | VLAN 200     | 192.168.1.66   | 255.255.255.224| 192.168.1.65   |
S2        |	VLAN 1       | 192.168.1.98   | 255.255.255.240|                |
PC-A	  | eth0         | DHCP           | DHCP           |	DHCP        |
PC-B	  | eth0         | DHCP           |	DHCP           |	DHCP        |
2. Создадим сеть и подключим устройства
#### Topology
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/DHCP/DHCP%20V4/Topology.png)
3. настроим базовые параметры роутеров R1 и R2.    
#### Пример конфигурации
hostname R1  
!  
line console 0  
password cisco  
exec-timeout 15 0  
login  
logging synchronous  
!  
line vty 0 4  
password cisco  
exec-timeout 15 0  
login  
logging synchronous  
!  
enable secret cisco  
!  
service password-encryption  
!  
banner motd $accessing the device that unauthorized access is  prohibited  
!  
no ip domain lookup  
!  
4. Сконфигурируем роутеры R1 и R2:
#### для R1 настроим
- субинтерфейсы на e0/1 согласно таблице
- маршрутизацию между vlan  
- интерфейс e 0/0
- дефолтный маршрут
#### для R2 настроим
- интерфейс e0/1 и e0/0 согласно таблице
- дефолтный маршрут
#### примеры конфигурации
#### R1
!  
interface e0/1  
no shutdown  
!  
interface e0/1.100  
encapsulation dot1Q 100  
ip address 192.168.1.1 255.255.255.192  
!  
interface e0/1.200  
encapsulation dot1Q 200  
ip address 192.168.1.65 255.255.255.224  
!  
interface e0/1.1000  
encapsulation dot1Q 1000 native  
!  
interface e0/0  
ip address 10.0.0.1 255.255.255.252  
!  
exit  
!  
ip route 0.0.0.0 0.0.0.0 10.0.0.2  
!  
end  
!
write  
!
#### R2  
!  
interface e0/0  
!  
no shutdown  
!  
ip address 10.0.0.2 255.255.255.252  
!  
interface e0/1  
!  
no shutdown  
!  
ip address 192.168.1.97 255.255.255.240  
!  
exit  
!  
ip route 0.0.0.0 0.0.0.0 10.0.0.1  
!  
end  
!  
write  
!  
5. Проверим настройки:  
- Убедимся, что субинтерфейсы настроены правильно:  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/DHCP/DHCP%20V4/interface.png)  
- Убедимся, что статическая маршрутизация работает, отправив эхо запрос c R1 на R2 :  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/DHCP/DHCP%20V4/ping%20R1%20to%20R2.png)  
6. Далее настроим базовые параметры каждого коммутатора.    
#### Пример конфигурации:  

hostname S1  
!  
line console 0  
password cisco  
exec-timeout 15 0  
login  
logging synchronous  
!  
line vty 0 4  
password cisco  
exec-timeout 15 0  
login  
logging synchronous  
!  
enable secret cisco  
!  
service password-encryption  
!  
banner motd $accessing the device that unauthorized access is  prohibited  
!  
no ip domain lookup  
!  
end  
!  
write  
7. 
- Согласно таблицам создадим и назовем необходимые vlan на S1. Настроим и активируем интерфейс управления на S1 (VLAN 200) и установим шлюз по умолчанию.  
- Настроим и активируем интерфейс управления на S2 (VLAN 1) и шлюз по умолчанию.
- Назначим все неиспользуемые порты на S1 в VLAN Parking_Lot, настроим для них режим статического доступа и отключим в административном порядке.  
- На S2 отключим все неиспользуемые порты в административном порядке.
#### Пример конфигурации:  
### S1
vlan 100  
!  
name Clients  
!  
vlan 200  
!  
name Management  
!  
vlan 999  
!  
name Parking_Lot  
!  
vlan 1000  
!  
name Native  
!  
exit  
!  
interface vlan 200  
!  
ip address 192.168.1.66 255.255.255.224  
!  
no shutdown  
!  
exit  
!
ip default-gateway 192.168.1.65  
!
interface range e0/0-1  
!  
switchport mode access  
!  
switchport access vlan 999  
!  
shutdown  
!  
exit  
!
### S2
!
interface vlan 1  
ip address 192.168.1.98 255.255.255.240  
no shutdown  
exit  
ip default-gateway 192.168.1.97  
interface range e0/0-1  
switchport mode access
shutdown   
exit  
!
8. Назначим используемые порты соответствующей VLAN и сконфигурируем их для статического режима доступа.
### S1
!  
interface e0/2  
switchport mode access  
switchport access vlan 100  
!  
Как видим порт e0/3 находится в сети VLAN по умолчанию и не настроен как магистральный порт 802.1Q.  

![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/DHCP/DHCP%20V4/vlan%20config.png)
9. Cконфигурируем интерфейс S1 e0/3 в качестве магистрального 802.1Q, установим для нативного VLAN значение 1000, разрешим для магистрали значения VLAN 100, 200 и 1000.  
!
interface e0/3  
switchport mode trunk  
switchport trunk native vlan 1000  
switchport trunk allowed vlan 100,200,1000  
end   
write  
!  
Проверим статус магистрали  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/DHCP/DHCP%20V4/trank%20config.png)

На данный момент у компьютера, если бы он был подключен к сети с использованием DHCP был бы автоматически настроенный адрес (APIPA) в диапазоне 169.254.x.x


# Часть 2. Настроим и проверим два DHCPv4-сервера на R1

1. Исключим первые пять доступных адресов из каждого пула адресов
2. Создадим пул DHCP с именем R1_Client_LAN, укажем сеть для DHCP-сервера, настроим доменное имя ccna-lab.com, шлюз по умолчанию и время аренды на 2 дня 12 часов и 30 минут.
3. Настройте второй пул DHCPv4, используя имя пула R2_Client_LAN укажем сеть для DHCP-сервера, шлюз по умолчанию, и аналогичное время аренды  
!  
ip dhcp excluded-address 192.168.1.1 192.168.1.5  
ip dhcp pool R1_Client_LAN  
network 192.168.1.0 255.255.255.192  
domain-name ccna-lab.com  
default-router 192.168.1.1  
lease 2 12 30  
!  
ip dhcp excluded-address 192.168.1.97 192.168.1.101
!
ip dhcp pool R2_Client_LAN
network 192.168.1.96 255.255.255.240
default-router 192.168.1.97
domain-name ccna-lab.com
lease 2 12 30
!
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/DHCP/DHCP%20V4/VPC-A.png)

