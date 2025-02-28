# Настройка маршрутизации в офисе Триада  
## Настроить IS-IS в ISP Триада  
#### Условия:  
R23 и R25 находятся в зоне 2222  
R24 находится в зоне 24  
R26 находится в зоне 26    
Топология:  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/IS-IS%20ISP%20TRIADA/IS-IS%20topology.png)  
### interfaces  
Device | interface | IP Address    | Note              |IP Loopback|	Provider    |
-------|-----------|---------------|-------------------|-----------|----------------|
R23    | e 0/0     |80.80.50.9/30  |to: e0/2 R22 KITORN|1.1.1.23   |TRIADA          |
R23    | e 0/1     |10.20.10.1/30  |to: e0/0 R25 TRIADA|           |                |
R23    | e 0/2     |10.20.10.5/30  |to: e0/2 R24 TRIADA|           |                |
R24    | e 0/0     |80.80.50.21/30 |to: e0/2 R21 LAMAS |1.1.1.24   |TRIADA          |
R24    | e 0/1     |10.20.10.13/30 |to: e0/0 R26 TRIADA|           |                |
R24    | e 0/2     |10.20.10.6/30  |to: e0/2 R23 TRIADA|           |                |
R24    | e 0/3     |80.80.50.81/30 |to: e0/2 R18 SPB   |           |                |
R25    | e 0/0     |10.20.10.2/30  |to: e0/1 R23 TRIADA|1.1.1.25   |TRIADA          |
R25    | e 0/1     |80.80.50.41/30 |to: e0/0 R27 LAB   |           |                |
R25    | e 0/2     |10.20.10.9/30  |to: e0/2 R26 TRIADA|           |                |
R25    | e 0/3     |80.80.50.53/30 |to: e0/1 R28 CHOK  |           |                |
R26    | e 0/0     |10.20.10.14/30 |to: e0/1 R24 TRIADA|1.1.1.26   |TRIADA          |
R26    | e 0/1     |80.80.50.61/30 |to: e0/0 R28 CHOK  |           |                |
R26    | e 0/2     |10.20.10.10/30 |to: e0/2 R25 TRIADA|           |                |
R26    | e 0/3     |80.80.50.73/30 |to: e0/3 R18 SPB   |           |                |
### Поднимем протокол IS-IS глобально на каждом роутере и зададим NET идентификатор, затем запустим IS-IS на интерфейсах:  
#### Для R23:  
router isis 100  
 net 49.2222.0000.0000.0000.0023.00  
!  
interface Loopback1  
 ip address 1.1.1.23 255.255.255.255  
 ip router isis 100  
 isis circuit-type level-2-only  
!  
interface Ethernet0/0  
 ip address 80.80.50.9 255.255.255.252  
 ip router isis 100  
 isis circuit-type level-1  
!  
interface Ethernet0/1  
 ip address 10.20.10.1 255.255.255.252  
 ip router isis 100  
 isis circuit-type level-2-only  
!  
interface Ethernet0/2  
 ip address 10.20.10.5 255.255.255.252  
 ip router isis 100  
 isis circuit-type level-2-only  

#### Для R24:  
!  
router isis 100  
 net 49.0024.0000.0000.0000.0024.00  
!  
interface Loopback1  
 ip address 1.1.1.24 255.255.255.255  
 ip router isis 100  
 isis circuit-type level-2-only  
!  
interface Ethernet0/0  
 ip address 80.80.50.21 255.255.255.252  
 ip router isis 100  
 isis circuit-type level-1  
!  
interface Ethernet0/1  
 ip address 10.20.10.13 255.255.255.252  
 ip router isis 100  
 isis circuit-type level-2-only  
!  
interface Ethernet0/2  
 ip address 10.20.10.6 255.255.255.252  
 ip router isis 100  
 isis circuit-type level-2-only  
!  
interface Ethernet0/3  
 ip address 80.80.50.81 255.255.255.252  
 ip router isis 100  
 isis circuit-type level-1  
!  
#### Для R25: 
!  
router isis 100  
 net 49.2222.0000.0000.0000.0025.00  
!  
interface Loopback1  
 ip address 1.1.1.25 255.255.255.255  
 ip router isis 100  
 isis circuit-type level-2-only  
!  
interface Ethernet0/0  
 ip address 10.20.10.2 255.255.255.252  
 ip router isis 100  
 isis circuit-type level-2-only  
!  
interface Ethernet0/1  
 ip address 80.80.50.41 255.255.255.252  
 ip router isis 100  
 isis circuit-type level-1  
!  
interface Ethernet0/2  
 ip address 10.20.10.9 255.255.255.252  
 ip router isis 100  
 isis circuit-type level-2-only  
!  
interface Ethernet0/3  
 ip address 80.80.50.53 255.255.255.252  
 ip router isis 100  
 isis circuit-type level-1  
!  
#### Для R26:  
!  
router isis 100  
 net 49.0026.0000.0000.0000.0026.00  
!  
interface Loopback1  
 ip address 1.1.1.26 255.255.255.255  
 ip router isis 100  
 isis circuit-type level-2-only  
!  
interface Ethernet0/0  
 ip address 10.20.10.14 255.255.255.252  
 ip router isis 100  
 isis circuit-type level-2-only  
!  
interface Ethernet0/1  
 ip address 80.80.50.61 255.255.255.252  
 ip router isis 100  
 isis circuit-type level-1  
!  
interface Ethernet0/2  
 ip address 10.20.10.10 255.255.255.252  
 ip router isis 100  
 isis circuit-type level-2-only  
!  
interface Ethernet0/3  
 ip address 80.80.50.73 255.255.255.252  
 ip router isis 100  
 isis circuit-type level-1  
!  
### Проверим формирование соседства на роутерах:  
#### Для R23, R25:  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/IS-IS%20ISP%20TRIADA/neighbors%20R23%20R25.png)  

#### Для R24, R26:  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/IS-IS%20ISP%20TRIADA/neighbors%20R24%20R26.png)  
