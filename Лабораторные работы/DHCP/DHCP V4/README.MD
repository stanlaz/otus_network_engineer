# Часть 1
1. Создадим таблицу адресации и рассчитаем подсети согласно заданию
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
S2        |	VLAN 1       | 192.168.1.98   | 255.255.255.240| 192.168.1.7    |
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
