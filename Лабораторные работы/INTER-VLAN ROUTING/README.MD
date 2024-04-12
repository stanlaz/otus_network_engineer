# Задание:
1. Создать сеть и настроить основные параметры устройств
2. Создать VLAN и назначить порты коммутаторов
3. Настроить магистрали 802.1Q между коммутаторами
4. Настроить маршрутизацию между VLAN на маршрутизаторе
5. Проверить работоспособность маршрутизации между VLAN

# Решение:
### 1. Создадим сеть и настроим основные параметры устройств

## Таблица адресации:

Устройство    | Интерфейс     | IP Адрес    | Маска подсети | Шлюз по умолчанию  
--------------| --------------| ------------| --------------| -----------
R1            | e0/2.3        |192.168.3.1  |255.255.255.0  | N/A
R1            | e0/2.4        |192.168.4.1  |255.255.255.0  | 
R1            | e0/2.8        |N/A          |N/A            |
S1            | VLAN3         |192.168.3.11 |255.255.255.0  |192.168.3.1
S2            | VLAN3         |192.168.3.12 |255.255.255.0  |192.168.3.1
PCA           | ETH0          |192.168.3.3  |255.255.255.0  |192.168.3.1
PCB           | ETH0          |192.168.4.3  |255.255.255.0  |192.168.4.1

## VLAN 3 управление

Графическая схема:  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/INTER-VLAN%20ROUTING/2024-04-12_14-33-49.png)

### 1.1 Настроим основные параметры безопасности на устройствах R1, S1, S2   
#### Пример конфигурации:  
!  
line console 0  
password cisco  
exec-timeout 5 0  
login  
logging synchronous  
!  
line vty 0 4  
password cisco  
exec-timeout 5 0  
login  
logging synchronous  
!  
enable secret cisco  
!  
service password-encryption  
!  
banner motd $accessing the device that unauthorized access is prohibited$  
### 2. Создадим VLAN и назначим порты коммутаторов
### 3. Настроим магистрали 802.1Q между коммутаторами  
#### Пример конфигурации:
* S1  
interface Ethernet0/0  
switchport access vlan 3  
switchport mode access  
!  
interface Ethernet0/1  
switchport trunk allowed vlan 3,4  
switchport trunk encapsulation dot1q  
switchport trunk native vlan 8  
switchport mode trunk  
!  
interface Ethernet0/2  
switchport trunk allowed vlan 3,4  
switchport trunk encapsulation dot1q  
switchport trunk native vlan 8  
switchport mode trunk  
spanning-tree portfast edge trunk  
!  
interface Ethernet0/3  
!  
interface Vlan3  
ip address 192.168.3.11 255.255.255.0  
!  
interface Vlan4  
no ip address  
!  
ip default-gateway 192.168.3.1  

### 4. Настроим маршрутизацию между VLAN  

Пример конфигурации:  
* R1  
interface Ethernet0/2.3  
encapsulation dot1Q 3  
ip address 192.168.3.1 255.255.255.0  
!  
interface Ethernet0/2.4  
encapsulation dot1Q 4  
ip address 192.168.4.1 255.255.255.0  
!  
interface Ethernet0/2.8  
encapsulation dot1Q 8 native  

### 5. Проверим работоспособность маршрутизации между VLAN

![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/INTER-VLAN%20ROUTING/2024-04-12_14-33-49.png)