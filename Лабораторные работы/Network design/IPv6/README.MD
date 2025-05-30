# Адресное пространство для IPv6
### IPv6 network  
![alt-текст](https://github.com/stanlaz/otus_network_engineer/blob/main/Лабораторные%20работы/Network%20design/IPv6/IPv6_Topology.png)  
### IPv6 Internet service provider table  
Device | interface | IP Address            | Note              |IP Loopback|	Provider    |  
-------|-----------|-----------------------|-------------------|-----------|----------------|  
R21    | e 0/0     |2024:ABAF:2021:2::1/64 |to: e0/2 R15 MSK   |3.3.3.22   |LAMAS           |   
R21    | e 0/1     |2024:ABAF:2021:1::1/64 |to: e0/1 R21 KITORN|           |                |
R21    | e 0/2     |2024:ABAD:1981:6::2/64 |to: e0/0 R24 TRIADA|           |                |
R22    | e 0/0     |2024:BACD:2020:1::1/64 |to: e0/2 R14 MSK   |2.2.2.21   |KITORN          |
R22    | e 0/1     |2024:ABAF:2021:1::2/64 |to: e0/1 R21 LAMAS |           |                |
R22    | e 0/2     |2024:ABAD:1981:7::2/64 |to: e0/0 R23 TRIADA|           |                |
R23    | e 0/0     |2024:ABAD:1981:7::1/64 |to: e0/2 R22 KITORN|1.1.1.23   |TRIADA          |
R23    | e 0/1     |2024:ABAD:1980:1::1/64 |to: e0/0 R25 TRIADA|           |                |
R23    | e 0/2     |2024:ABAD:1980:2::1/64 |to: e0/2 R24 TRIADA|           |                |
R24    | e 0/0     |2024:ABAD:1981:6::1/64 |to: e0/2 R21 LAMAS |1.1.1.24   |TRIADA          |
R24    | e 0/1     |2024:ABAD:1980:3::1/64 |to: e0/0 R26 TRIADA|           |                |
R24    | e 0/2     |2024:ABAD:1980:2::2/64 |to: e0/2 R23 TRIADA|           |                |
R24    | e 0/3     |2024:ABAD:1981:1::1/64 |to: e0/2 R18 SPB   |           |                |
R25    | e 0/0     |2024:ABAD:1980:1::2/64 |to: e0/1 R23 TRIADA|1.1.1.25   |TRIADA          |
R25    | e 0/1     |2024:ABAD:1981:5::1/64 |to: e0/0 R27 LAB   |           |                |
R25    | e 0/2     |2024:ABAD:1980:4::1/64 |to: e0/2 R26 TRIADA|           |                |
R25    | e 0/3     |2024:ABAD:1981:4::1/64 |to: e0/1 R28 CHOK  |           |                |

### IPv6 Moscow table  
Device | interface | IP Address            | Note              |IP Loopback|  
-------|-----------|-----------------------|-------------------|-----------|  
R12    | e 0/0     |2024:BACD:2020:7::1/64 |to: e1/0 SW4       |4.4.4.12   |   
R12    | e 0/1     |2024:BACD:2020:8::2/64 |to: e1/1 SW5       |           |
R12    | e 0/2     |2024:BACD:2020:2::2/64 |to: e0/0 R14       |           |
R12    | e 0/3     |2024:BACD:2020:4::2/64 |to: e0/1 R15       |           |
R13    | e 0/0     |2024:BACD:2020:8::1/64 |to: e1/0 SW5       |4.4.4.13   |
R13    | e 0/1     |2024:BACD:2020:7::2/64 |to: e1/1 SW4       |           |
R13    | e 0/2     |2024:BACD:2020:5::2/64 |to: e0/0 R15       |           |
R13    | e 0/3     |2024:BACD:2020:3::2/64 |to: e0/1 R14       |           |
R14    | e 0/0     |2024:BACD:2020:2::1/64 |to: e0/2 R12       |4.4.4.14   |
R14    | e 0/1     |2024:BACD:2020:3::1/64 |to: e0/3 R13       |           |
R14    | e 0/2     |2024:BACD:2020:1::2/64 |to: e0/0 R22 KITORN|           |
R14    | e 0/3     |2024:BACD:2020:9::1/64 |to: e0/0 R19       |           |
R15    | e 0/0     |2024:BACD:2020:5::1/64 |to: e0/2 R13       |4.4.4.15   |
R15    | e 0/1     |2024:BACD:2020:4::1/64 |to: e0/3 R12       |           |
R15    | e 0/2     |2024:ABAF:2021:2::2/64 |to: e0/0 R21 LAMAS |           |
R15    | e 0/3     |2024:BACD:2020:6::1/64 |to: e0/0 R20       |           |
R19    | e 0/0     |2024:BACD:2020:2::9/64 |to: e0/3 R14       |4.4.4.19   |
R20    | e 0/0     |2024:BACD:2020:6::2/64 |to: e0/3 R15       |4.4.4.20   |  

### IPv6 SPB table  
Device | interface | IP Address            | Note              |IP Loopback|  
-------|-----------|-----------------------|-------------------|-----------|  
R16    | e 0/0     |2024:ABAD:1981:12::2/64|t0: e0/3 SW10      |           |  
R16    | e 0/1     |2024:ABAD:1981:13::2/64|t0: e0/0 R18       |           |  
R16    | e 0/2     |2024:ABAD:1981:11::2/64|t0: e1/0 SW9       |           |  
R16    | e 0/3     |2024:ABAD:1981:14::1/64|t0: e0/0 R32       |           |  
R17    | e 0/0     |2024:ABAD:1981:11::1/64|t0: e0/3 SW9       |           |  
R17    | e 0/1     |2024:ABAD:1981:10::2/64|t0: e0/1 R18       |           |  
R17    | e 0/2     |2024:ABAD:1981:12::1/64|to: e1/0 SW10      |           |  
R18    | e 0/0     |2024:ABAD:1981:13::1/64|to: e0/1 R16       |           |  
R18    | e 0/1     |2024:ABAD:1981:10::1/64|to: e0/1 R17       |           |  
R18    | e 0/2     |2024:ABAD:1981:1::2/64 |to: e0/3 R24 TRIADA|           |  
R18    | e 0/3     |2024:ABAD:1981:2::2/64 |to: e0/3 R26 TRIADA|           |  
R32    | e 0/0     |2024:ABAD:1981:14::2/64|to: e0/3 R16       |           |  

### IPv6 LABYTNAGY table  
Device | interface | IP Address            | Note              |IP Loopback|  
-------|-----------|-----------------------|-------------------|-----------|  
R16    | e 0/0     |2024:ABAD:1981:5::2/64 |to: e0/1 R25       |           |   

### IPv6 CHOKURDAH table  
Device | interface | IP Address            | Note              |IP Loopback|  
-------|-----------|-----------------------|-------------------|-----------|  
R16    | e 0/0     |2024:ABAD:1981:3::2/64 |to: e0/1 R26       |           |  
R16    | e 0/1     |2024:ABAD:1981:4::2/64 |to: e0/3 R25       |           |  
R16    | e 0/2     |2024:ABAD:1981:15::1/64|to: e0/2 SW29      |           |  





  

