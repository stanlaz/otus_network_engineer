# Маршрутизация на основе политик (PBR)  
## Цель: Настроить политику маршрутизации в офисе Чокурдах, распределить трафик между 2 линками  

### Часть 1: Настроить политику маршрутизации для сетей офиса Чокурдах  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/PBR%20SLA/topology.png)

#### Device interfaces  
Device | interface | IP Address    | Note              |IP Loopback|
-------|-----------|---------------|-------------------|-----------|
R25    | e 0/0     |10.20.10.2/30  |to: e0/1 R23 TRIADA|1.1.1.25   |
R25    | e 0/1     |80.80.50.41/30 |to: e0/0 R27 LAB   |           |
R25    | e 0/2     |10.20.10.9/30  |to: e0/2 R26 TRIADA|           |
R25    | e 0/3     |80.80.50.53/30 |to: e0/1 R28 CHOK  |           |
R26    | e 0/0     |10.20.10.14/30 |to: e0/1 R24 TRIADA|1.1.1.26   |
R26    | e 0/1     |80.80.50.61/30 |to: e0/0 R28 CHOK  |           |
R26    | e 0/2     |10.20.10.10/30 |to: e0/2 R25 TRIADA|           |
R26    | e 0/3     |80.80.50.73/30 |to: e0/3 R18 SPB   |           |
R27    | e 0/0     |80.80.50.42/30 |t0: e0/1 R25 TRIADA|6.6.6.27   |
R28    | e 0/0     |80.80.50.62/30 |t0: e0/1 R26 TRIADA|7.7.7.28   |
R28    | e 0/1     |80.80.50.54/30 |t0: e0/3 R25 TRIADA|           |
R28    | e 0/2     |172.27.50.1/24 |t0: e0/2 SW29 CHOK |           |
VPC30  | eth0      |172.27.50.5/24 |to: e0/0 SW29 CHOK |           |
VPC31  | eth0      |172.27.50.6/24 |to: e0/0 SW29 CHOK |           |

#### Настроим статические маршруты для R28:    

ip route 0.0.0.0 0.0.0.0 80.80.50.53 10  
ip route 0.0.0.0 0.0.0.0 80.80.50.61 10  

### Часть2: Распределить трафик между двумя линками с провайдером в Чокурдах  
#### Создадим на R28 route-map посредством Access-list для VPC30 и VPC31:  

ip access-list standard VPC30  
 permit 172.27.50.5  
ip access-list standard VPC31  
 permit 172.27.50.6  
!  
route-map VPC permit 10  
 match ip address VPC30  
 set ip next-hop 80.80.50.61  
!  
route-map VPC permit 20  
 match ip address VPC31  
 set ip next-hop 80.80.50.53  
!  
route-map VPC deny 30  
!  
### Часть3: Настроить отслеживание линка через технологию IP SLA в Чокурдах  
#### Настроим функцию отслеживания доступности интерфейсов R25 и R26 с помощью ICMP-echo  
ip sla 1  
 icmp-echo 80.80.50.61 source-ip 80.80.50.62  
 frequency 10  
ip sla schedule 1 life forever start-time now  
ip sla 2  
 icmp-echo 80.80.50.53 source-ip 80.80.50.54  
 frequency 11  
ip sla schedule 2 life forever start-time now  

### Часть4: Настроить для офиса Лабытнанги маршрут по-умолчанию  
#### Для R27 создадим статический маршрут:  
ip route 0.0.0.0 0.0.0.0 80.80.50.41  

### Часть5: План работы и изменения зафиксированы в документации  
#### Проверим балансировку маршрутов для VPC30 и VPC31:  

![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/PBR%20SLA/trace_VPC.png)  

Проверим функцию отслеживания IP-SLA:  

![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/PBR%20SLA/sla_stat.png)  