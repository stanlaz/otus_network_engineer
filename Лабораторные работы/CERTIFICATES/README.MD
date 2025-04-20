# IPSec over DmVPN  
### Цель:  
## Настроить GRE поверх IPSec между офисами Москва и С.-Петербург  
## Настроить DMVPN поверх IPSec между офисами Москва и Чокурдах, Лабытнанги  
### Для IPSec использовать CA и сертификаты.  

## Часть 1: настроим СА и выдадим сертификаты.  
В качестве центра сертификации используем R14 (MOSCOW), настроим http и pki server, сгенерируем rsa-ключ:  
!  
ip http server  
ip domain name OTUS  
crypto key generate rsa general-keys label R14 modulus 2048 exportable  
crypto pki server R14  
database level complete  
no shutdown (Password: cisco159)  
!  
Для R15, R18, R27 и R28 сгенерируем rsa-ключ, настроим trustpoint, укажем URL для запроса сертификатов:  
!  
ip domain name OTUS  
ip host R14 4.4.4.14  
crypto key generate rsa (2048 bits)  
crypto pki trustpoint R14  
enrollment url http://R14:80  
crypto pki authenticate R14  
crypto pki enroll R14 (enter password)  
!  
#### процесс запроса сертификата:  
% The subject name in the certificate will include: R27.OTUS  
% Include the router serial number in the subject name? [yes/no]: y  
% The serial number in the certificate will be: 67109296  
% Include an IP address in the subject name? [no]: y  
Enter Interface name or IP Address[]:  
% Skipping IP address  
!  
Request certificate from CA? [yes/no]: y  
% Certificate request sent to Certificate Authority  
% The 'show crypto pki certificate verbose R14' commandwill show the fingerprint.  
#### далее на R14 подпишем запросы на сертификаты:  
crypto pki server R14 grant 1  
crypto pki server R14 grant 2  
crypto pki server R14 grant 3  
crypto pki server R14 grant 4  
#### проверим выданные сертификаты  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/CERTIFICATES/R14-CERTIFICATES.png)  
!  
## Часть 2: Настроим IKE-политики и IPSec профиль:  
!  
#### Для R15 - R18:  
crypto isakmp enable  
crypto isakmp policy 10  
 encr aes  
 hash sha256  
 group 16  
 lifetime 3600   
!  
crypto ipsec transform-set VTI esp-aes esp-sha-hmac  
mode transport  
crypto ipsec profile MOSCOW  
set transform-set VTI  
!  
#### Для R15 - R27,R28:  
crypto isakmp enable  
crypto isakmp policy 15  
 encr aes  
 hash sha256  
 group 16  
 lifetime 3600  
!  
crypto ipsec transform-set VTI esp-aes esp-sha-hmac  
mode transport  
crypto ipsec profile MOSCOW-1  
set transform-set VTI  
!  
## Часть 3:  привяжем наши политики к существующим тунельным интерфейсам:  
#### для interface Tunnel1:  
!  
tunnel protection ipsec profile MOSCOW  
!  
#### для interface Tunnel2:  
!  
tunnel protection ipsec profile MOSCOW-1  
!  
## Часть 4: проверка:  
#### вывод состояния для phase-2 IPSec:  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/CERTIFICATES/IPSec-SA-R15-R18.png)  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/CERTIFICATES/IPSec-SA-R27-R28.png)  
#### IP связность:  
!  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/CERTIFICATES/ECHO-VPC7.png)  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/CERTIFICATES/TRACE-TO-VPC1.png)  
