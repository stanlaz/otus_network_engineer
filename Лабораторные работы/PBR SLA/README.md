# Маршрутизация на основе политик (PBR)  
## Цель: Настроить политику маршрутизации в офисе Чокурдах Распределить трафик между 2 линками  

### Часть 1: Настроить политику маршрутизации для сетей офиса Чокурдах  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/PBR%20SLA/topology.png)

Device | interface | IP Address    | Note              |IP Loopback|  
-------|-----------|---------------|-------------------|-----------|  
R27    | e 0/0     |80.80.50.42/30 |t0: e0/1 R25       |6.6.6.27   |  
R28    | e 0/0     |80.80.50.62/30 |t0: e0/1 R26       |7.7.7.28   |  
R28    | e 0/1     |80.80.50.54/30 |t0: e0/3 R25       |           |  
R28    | e 0/2     |172.27.50.1/24 |t0: e0/2 SW29      |           |  
VPC30  | eth0      |172.27.50.5/24 |to: e0/0 SW29      |           |  
VPC31  | eth0      |172.27.50.6/24 |to: e0/0 SW29      |           |  

На R28 у нас наcтроено четыре интерфейса:  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/PBR%20SLA/interfaces.png)  
На R25 со стороны провайдера  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/PBR%20SLA/R25_Interface.png)  

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
Для R27 создадим статический маршрут:  
ip route 0.0.0.0 0.0.0.0 80.80.50.41  

### Часть5: План работы и изменения зафиксированы в документации  
Проверим балансировку маршрутов для VPC30 и VPC31:  

![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/PBR%20SLA/trace_VPC.png)  

Проверим функцию отслеживания IP-SLA:  

![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/PBR%20SLA/sla_stat.png)  