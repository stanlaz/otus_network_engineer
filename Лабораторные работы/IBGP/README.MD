# Масштабируемость и дизайн iBGP  
## iBGP  

### Цель:  

- Настроите iBGP в офисе Москва между маршрутизаторами R14 и R15  
- Настроите iBGP в провайдере Триада, с использованием RR  
- Настройте офиса Москва так, чтобы приоритетным провайдером стал Ламас  
- Настройте офиса С.-Петербург так, чтобы трафик до любого офиса распределялся по двум линкам одновременно  
- Все сети в лабораторной работе должны иметь IP связность
#### Topology:  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/IBGP/iBGP-Topology.png)  

### Часть 1: Настроим iBGP на роутерах в офисе Москвы (на примере R14 и R15):  
R14:    
router bgp 1001  
 bgp router-id 4.4.4.14  
 bgp log-neighbor-changes  
 network 4.4.4.14 mask 255.255.255.255  
 neighbor 4.4.4.12 remote-as 1001  
 neighbor 4.4.4.12 update-source Loopback1  
 neighbor 4.4.4.13 remote-as 1001  
 neighbor 4.4.4.13 update-source Loopback1  
 

R15:  
router bgp 1001  
 bgp router-id 4.4.4.15  
 bgp log-neighbor-changes  
 network 4.4.4.15 mask 255.255.255.255  
 neighbor 4.4.4.12 remote-as 1001  
 neighbor 4.4.4.12 update-source Loopback1  
 neighbor 4.4.4.13 remote-as 1001  
 neighbor 4.4.4.13 update-source Loopback1  
  

### Часть 1: Настроим iBGP в провайдере Триада с использованием RR (AS520), в качестве Route Reflector выберем R23, настроим группу соседей AS520:   

R23:  
router bgp 520  
 bgp router-id 1.1.1.23  
 bgp log-neighbor-changes  
 network 1.1.1.23 mask 255.255.255.255  
 neighbor AS-520 peer-group  
 neighbor AS-520 remote-as 520  
 neighbor AS-520 update-source Loopback1  
 neighbor AS-520 route-reflector-client  
 neighbor AS-520 next-hop-self  
 neighbor 1.1.1.24 peer-group AS-520  
 neighbor 1.1.1.25 peer-group AS-520  
 neighbor 1.1.1.26 peer-group AS-520  

#### применим политики группы к остальным соседям  
R24:  
router bgp 520  
 bgp router-id 1.1.1.24  
 bgp log-neighbor-changes  
 network 1.1.1.24 mask 255.255.255.255  
 neighbor AS-520 peer-group  
 neighbor AS-520 remote-as 520  
 neighbor AS-520 update-source Loopback1  
 neighbor 1.1.1.23 peer-group AS-520  
 neighbor 80.80.50.22 remote-as 301  
 neighbor 80.80.50.82 remote-as 2042  
 
 Проверим формирование соседства:  
 ![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/IBGP/TRIADA-iBGB-NEIG.png)  




