# Лабораторная работа. Развертывание коммутируемой сети с резервными каналами

## Цели:  
Часть 1. Создание сети и настройка основных параметров устройства  
Часть 2. Выбор корневого моста  
Часть 3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов  
Часть 4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов  

# Часть 1.  
### Создадим сеть согласно топологии:
#### Таблица адресации
Устройство | Интерфейс | IP-адрес   | Маска подсети 
-----------|-----------|------------|----------------
S1         | VLAN 1    | 192.168.1.1| 255.255.255.0  
S2         | VLAN 1    | 192.168.1.2| 255.255.255.0
S3         | VLAN 1    | 192.168.1.3| 255.255.255.0

#### Топология
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/STP/topology.png)

Далее настроим базовые параметры каждого коммутатора и зададим ip адреса согласно таблице адрессации.    
Пример конфигурации:  
 hostname S2  
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
vlan1  
interface Vlan1  
 ip address 192.168.1.2 255.255.255.0  
!  
 write  

Теперь проверим способность коммутаторов обмениваться эхо-запросами

![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/STP/ping%20S1%20to%20S2%2C%20S3.png)  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/STP/ping%20S2%20to%20S3.png)  
Как видим эхо запросы успешно выполняются.  

# Часть 2.  
### Определим корневой мост
Для этого: 
* Отключим все порты на коммутаторах.  
* Настроим подключенные порты в качестве транковых.
* Включим порты e0/1 и e0/3 на всех коммутаторах.
* Отобразим данные протокола spanning-tree.
#### Пример конфигурации:

interface range e 0/0-3  
!  
shutdown  
!  
switchport trunk allowed vlan 1  
!  
switchport trunk encapsulation dot1q  
!  
switchport mode trunk  
!
switchport trunk allowed vlan 1  
!  
interface range e0/1, e0/3  
!  
no shutdown  
#### конфигурация STP на коммутаторах
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/STP/show_stp.png)  

