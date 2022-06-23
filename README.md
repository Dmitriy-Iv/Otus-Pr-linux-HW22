# **Введение**

В данном домашнем задании нам необходимо получить практический начальный опыт работы с VPN/OpenVPN в Linux.

---

# Подготовка тестового стенда
1. Нам необходимо поднять два виртуальных сервера, один из которых будет клиентом, второй сервером. Для этого запустим наш стенд.
```
git clone https://github.com/Dmitriy-Iv/Otus-Pr-linux-HW22.git
cd Otus-Pr-linux-HW22/tun-tap-vpn/
vagrant up
```

# TUN/TAP режимы VPN
1. Разница между режимами TUN и TAP в том, что они работают на разных уровнях модели OSI. TAP эмулирует Ethernet устройство и работает на канальном уровне модели OSI, оперируя кадрами Ethernet. TUN (сетевой туннель) работает на сетевом уровне модели OSI, оперируя IP пакетами. TAP используется для создания сетевого моста, тогда как TUN для маршрутизации. Простыми словами - если нам необходимо растянуть один сегмент L2 с одной адресацией (vlan - широковещательный домен), то это - TAP. Если например объединить две сети с разной адресацией - то это TUN.

2. Проверяем разницу в скорости в разных режимах работы туннеля. Тестируем первым TAP.
На сервере запускаем iperf3 в режиме сервер, а на клиенте в режиме клиент с указанием ip сервера, продолжительности проверки в секундах `-t` интервалом вывода информации на экран `-i`.
```
[root@server ~]# ip a | grep tap0
12: tap0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 100
    inet 10.10.10.1/24 brd 10.10.10.255 scope global tap0

[root@server ~]# iperf3 -s &
[1] 7484
[root@server ~]# -----------------------------------------------------------
Server listening on 5201
-----------------------------------------------------------
Accepted connection from 10.10.10.2, port 32948
[  5] local 10.10.10.1 port 5201 connected to 10.10.10.2 port 32950
[ ID] Interval           Transfer     Bandwidth
[  5]   0.00-1.00   sec  14.9 MBytes   125 Mbits/sec
[  5]   1.00-2.00   sec  15.0 MBytes   126 Mbits/sec
[  5]   2.00-3.00   sec  15.3 MBytes   128 Mbits/sec
[  5]   3.00-4.00   sec  14.9 MBytes   125 Mbits/sec
[  5]   4.00-5.00   sec  15.3 MBytes   129 Mbits/sec
[  5]   5.00-6.00   sec  15.3 MBytes   128 Mbits/sec
[  5]   6.00-7.00   sec  15.7 MBytes   132 Mbits/sec
[  5]   7.00-8.00   sec  15.2 MBytes   128 Mbits/sec
[  5]   8.00-9.00   sec  15.0 MBytes   126 Mbits/sec
[  5]   9.00-10.00  sec  15.6 MBytes   131 Mbits/sec
[  5]  10.00-11.00  sec  15.4 MBytes   129 Mbits/sec
[  5]  11.00-12.00  sec  15.1 MBytes   127 Mbits/sec
[  5]  12.00-13.00  sec  15.4 MBytes   129 Mbits/sec
[  5]  13.00-14.00  sec  15.8 MBytes   132 Mbits/sec
[  5]  14.00-15.00  sec  15.6 MBytes   131 Mbits/sec
[  5]  15.00-16.00  sec  15.3 MBytes   128 Mbits/sec
[  5]  16.00-17.00  sec  15.3 MBytes   129 Mbits/sec
[  5]  17.00-18.00  sec  15.7 MBytes   131 Mbits/sec
[  5]  18.00-19.00  sec  15.5 MBytes   130 Mbits/sec
[  5]  19.00-20.00  sec  15.0 MBytes   126 Mbits/sec
[  5]  20.00-21.00  sec  15.6 MBytes   131 Mbits/sec
[  5]  21.00-22.00  sec  14.9 MBytes   125 Mbits/sec
[  5]  22.00-23.00  sec  16.0 MBytes   134 Mbits/sec
[  5]  23.00-24.01  sec  15.2 MBytes   127 Mbits/sec
[  5]  24.01-25.00  sec  15.8 MBytes   133 Mbits/sec
[  5]  25.00-26.00  sec  15.4 MBytes   130 Mbits/sec
[  5]  26.00-27.00  sec  15.5 MBytes   130 Mbits/sec
[  5]  27.00-28.00  sec  15.7 MBytes   131 Mbits/sec
[  5]  28.00-29.00  sec  15.7 MBytes   132 Mbits/sec
[  5]  29.00-30.00  sec  15.8 MBytes   133 Mbits/sec
[  5]  30.00-31.00  sec  14.7 MBytes   123 Mbits/sec
[  5]  31.00-32.00  sec  15.5 MBytes   130 Mbits/sec
[  5]  32.00-33.00  sec  15.4 MBytes   129 Mbits/sec
[  5]  33.00-34.00  sec  15.5 MBytes   130 Mbits/sec
[  5]  34.00-35.00  sec  15.3 MBytes   128 Mbits/sec
[  5]  35.00-36.00  sec  14.8 MBytes   124 Mbits/sec
[  5]  36.00-37.00  sec  15.6 MBytes   131 Mbits/sec
[  5]  37.00-38.00  sec  15.2 MBytes   127 Mbits/sec
[  5]  38.00-39.00  sec  15.5 MBytes   130 Mbits/sec
[  5]  39.00-40.00  sec  14.9 MBytes   126 Mbits/sec
[  5]  40.00-40.07  sec  1.01 MBytes   126 Mbits/sec
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth
[  5]   0.00-40.07  sec  0.00 Bytes  0.00 bits/sec                  sender
[  5]   0.00-40.07  sec   615 MBytes   129 Mbits/sec                  receiver
-----------------------------------------------------------
Server listening on 5201
-----------------------------------------------------------

[root@client ~]# iperf3 -c 10.10.10.1 -t 40 -i 5
Connecting to host 10.10.10.1, port 5201
[  4] local 10.10.10.2 port 32950 connected to 10.10.10.1 port 5201
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-5.00   sec  77.5 MBytes   130 Mbits/sec  223    188 KBytes
[  4]   5.00-10.00  sec  76.9 MBytes   129 Mbits/sec   55    257 KBytes
[  4]  10.00-15.00  sec  77.4 MBytes   130 Mbits/sec   52    334 KBytes
[  4]  15.00-20.00  sec  76.8 MBytes   129 Mbits/sec    8    350 KBytes
[  4]  20.00-25.00  sec  77.4 MBytes   130 Mbits/sec   52    295 KBytes
[  4]  25.00-30.00  sec  77.8 MBytes   131 Mbits/sec   15    378 KBytes
[  4]  30.00-35.00  sec  76.8 MBytes   129 Mbits/sec   20    369 KBytes
[  4]  35.00-40.00  sec  76.1 MBytes   128 Mbits/sec    7    344 KBytes
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-40.00  sec   617 MBytes   129 Mbits/sec  432             sender
[  4]   0.00-40.00  sec   615 MBytes   129 Mbits/sec                  receiver

iperf Done.
```
3. Тестируем TUN. Для этого нам необходимо в файле `main.yml` изменить значение переменной `vpn_mode` с `tap` на `tun` и заново запустить playbook. После этого провести аналогичные измерения скорости.
```
[root@server ~]# ip a | grep tun0
13: tun0: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 100
    inet 10.10.10.1/24 brd 10.10.10.255 scope global tun0

[root@server ~]# iperf3 -s &
[2] 10422
[root@server ~]# iperf3: error - unable to start listener for connections: Address already in use
iperf3: exiting
Accepted connection from 10.10.10.2, port 32952
[  5] local 10.10.10.1 port 5201 connected to 10.10.10.2 port 32954
[ ID] Interval           Transfer     Bandwidth
[  5]   0.00-1.00   sec  13.3 MBytes   112 Mbits/sec
[  5]   1.00-2.00   sec  14.1 MBytes   119 Mbits/sec
[  5]   2.00-3.00   sec  13.9 MBytes   117 Mbits/sec
[  5]   3.00-4.00   sec  14.0 MBytes   118 Mbits/sec
[  5]   4.00-5.00   sec  14.5 MBytes   121 Mbits/sec
[  5]   5.00-6.00   sec  15.8 MBytes   133 Mbits/sec
[  5]   6.00-7.00   sec  15.4 MBytes   129 Mbits/sec
[  5]   7.00-8.00   sec  15.8 MBytes   133 Mbits/sec
[  5]   8.00-9.00   sec  15.4 MBytes   129 Mbits/sec
[  5]   9.00-10.00  sec  15.4 MBytes   129 Mbits/sec
[  5]  10.00-11.00  sec  15.4 MBytes   129 Mbits/sec
[  5]  11.00-12.00  sec  15.4 MBytes   129 Mbits/sec
[  5]  12.00-13.00  sec  15.3 MBytes   129 Mbits/sec
[  5]  13.00-14.00  sec  15.7 MBytes   132 Mbits/sec
[  5]  14.00-15.00  sec  15.5 MBytes   130 Mbits/sec
[  5]  15.00-16.00  sec  15.3 MBytes   128 Mbits/sec
[  5]  16.00-17.00  sec  15.4 MBytes   129 Mbits/sec
[  5]  17.00-18.00  sec  15.5 MBytes   130 Mbits/sec
[  5]  18.00-19.00  sec  15.5 MBytes   130 Mbits/sec
[  5]  19.00-20.00  sec  15.8 MBytes   133 Mbits/sec
[  5]  20.00-21.00  sec  15.7 MBytes   132 Mbits/sec
[  5]  21.00-22.00  sec  14.3 MBytes   120 Mbits/sec
[  5]  22.00-23.01  sec  15.8 MBytes   132 Mbits/sec
[  5]  23.01-24.00  sec  15.4 MBytes   130 Mbits/sec
[  5]  24.00-25.00  sec  15.7 MBytes   131 Mbits/sec
[  5]  25.00-26.00  sec  14.3 MBytes   120 Mbits/sec
[  5]  26.00-27.00  sec  15.4 MBytes   129 Mbits/sec
[  5]  27.00-28.00  sec  14.2 MBytes   119 Mbits/sec
[  5]  28.00-29.01  sec  14.3 MBytes   120 Mbits/sec
[  5]  29.01-30.00  sec  15.5 MBytes   131 Mbits/sec
[  5]  30.00-31.00  sec  15.3 MBytes   129 Mbits/sec
[  5]  31.00-32.00  sec  14.0 MBytes   118 Mbits/sec
[  5]  32.00-33.00  sec  14.5 MBytes   121 Mbits/sec
[  5]  33.00-34.00  sec  14.0 MBytes   117 Mbits/sec
[  5]  34.00-35.00  sec  15.3 MBytes   128 Mbits/sec
[  5]  35.00-36.00  sec  15.4 MBytes   129 Mbits/sec
[  5]  36.00-37.00  sec  15.4 MBytes   129 Mbits/sec
[  5]  37.00-38.00  sec  15.2 MBytes   128 Mbits/sec
[  5]  38.00-39.00  sec  15.6 MBytes   131 Mbits/sec
[  5]  39.00-40.00  sec  15.2 MBytes   128 Mbits/sec
[  5]  40.00-40.05  sec   778 KBytes   128 Mbits/sec
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth
[  5]   0.00-40.05  sec  0.00 Bytes  0.00 bits/sec                  sender
[  5]   0.00-40.05  sec   604 MBytes   126 Mbits/sec                  receiver
-----------------------------------------------------------
Server listening on 5201
-----------------------------------------------------------
[2]+  Exit 1                  iperf3 -s

[root@client ~]# iperf3 -c 10.10.10.1 -t 40 -i 5
Connecting to host 10.10.10.1, port 5201
[  4] local 10.10.10.2 port 32954 connected to 10.10.10.1 port 5201
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-5.01   sec  71.4 MBytes   120 Mbits/sec   80    240 KBytes
[  4]   5.01-10.00  sec  77.9 MBytes   131 Mbits/sec   75    322 KBytes
[  4]  10.00-15.00  sec  77.4 MBytes   130 Mbits/sec  182    267 KBytes
[  4]  15.00-20.00  sec  77.5 MBytes   130 Mbits/sec  103    201 KBytes
[  4]  20.00-25.00  sec  77.0 MBytes   129 Mbits/sec   52    223 KBytes
[  4]  25.00-30.00  sec  73.5 MBytes   123 Mbits/sec   56    210 KBytes
[  4]  30.00-35.00  sec  73.4 MBytes   123 Mbits/sec   93    174 KBytes
[  4]  35.00-40.00  sec  76.5 MBytes   128 Mbits/sec   12    317 KBytes
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-40.00  sec   605 MBytes   127 Mbits/sec  653             sender
[  4]   0.00-40.00  sec   604 MBytes   127 Mbits/sec                  receiver
iperf Done.
```
Подводя итоги замеров мы видим - в режиме TAP что за 40 секунд прокачалось 615 MBytes со средней скоростью 129 Mbits/sec, в режиме TUN  прокачалось 604 MBytes со средней скоростью 127 Mbits/sec. Вывод исходя из этих замеров можно сделать такой - разница в производительности туннеля в данных условиях минимальна, но TAP производительней.

# RAS на базе OpenVPN
1. Для запуска этой лабы заходим в папку `openvpn` и запускаем другой Vagrantfile.
```
git clone https://github.com/Dmitriy-Iv/Otus-Pr-linux-HW22.git
cd Otus-Pr-linux-HW22/openvpn/
vagrant up
```
2. В этой части задания нам необходмо было проверить работу RAS на базе OpenVPN. Для этого возьмём за основу те же два виртуальных сервера: server и client. Только добавим к ним ещё по одному интерфейсу с локальными сетями: 172.16.10.0/24 и 172.16.20.0/24. Это для эмуляции того, что за нашим server и client есть ещё сети и между ними будет ходить маршрутизация. Для того, чтобы это работало мы добавлем в конфиг сервера:
```
route 172.16.20.0 255.255.255.0
push "route 172.16.10.0 255.255.255.0"
```
А также создаём файл `/etc/openvpn/client/client` со следующим содержимым.
```
iroute 172.16.20.0 255.255.255.0
```
3. После запуска стенда проверяем маршрутизацию.
- На сервере:
```
dima@Test-Ubuntu-1:~/otus/my-hw22/openvpn$ vagrant ssh server
Last login: Thu Jun 23 18:11:34 2022 from 192.168.56.1
[vagrant@server ~]$ sudo -i
[root@server ~]# ip route
default via 10.0.2.2 dev eth0 proto dhcp metric 101
10.0.2.0/24 dev eth0 proto kernel scope link src 10.0.2.15 metric 101
10.10.10.0/24 via 10.10.10.2 dev tun0
10.10.10.2 dev tun0 proto kernel scope link src 10.10.10.1
172.16.10.0/24 dev eth3 proto kernel scope link src 172.16.10.1 metric 103
172.16.20.0/24 via 10.10.10.2 dev tun0
192.168.10.0/24 dev eth1 proto kernel scope link src 192.168.10.10 metric 100
192.168.56.0/24 dev eth2 proto kernel scope link src 192.168.56.10 metric 102
[root@server ~]# ping -c 4 172.16.20.1
PING 172.16.20.1 (172.16.20.1) 56(84) bytes of data.
64 bytes from 172.16.20.1: icmp_seq=1 ttl=64 time=1.07 ms
64 bytes from 172.16.20.1: icmp_seq=2 ttl=64 time=0.650 ms
64 bytes from 172.16.20.1: icmp_seq=3 ttl=64 time=1.07 ms
64 bytes from 172.16.20.1: icmp_seq=4 ttl=64 time=1.06 ms

--- 172.16.20.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3002ms
rtt min/avg/max/mdev = 0.650/0.966/1.075/0.186 ms
```
- На клиенте:
```
dima@Test-Ubuntu-1:~/otus/my-hw22/openvpn$ vagrant ssh client
Last login: Thu Jun 23 18:11:34 2022 from 192.168.56.1
[vagrant@client ~]$ sudo -i
[root@client ~]# ip route
default via 10.0.2.2 dev eth0 proto dhcp metric 103
10.0.2.0/24 dev eth0 proto kernel scope link src 10.0.2.15 metric 103
10.10.10.0/24 via 10.10.10.5 dev tun0
10.10.10.5 dev tun0 proto kernel scope link src 10.10.10.6
172.16.10.0/24 via 10.10.10.5 dev tun0
172.16.20.0/24 dev eth3 proto kernel scope link src 172.16.20.1 metric 102
192.168.10.0/24 dev eth1 proto kernel scope link src 192.168.10.20 metric 100
192.168.56.0/24 dev eth2 proto kernel scope link src 192.168.56.20 metric 101
[root@client ~]# ping -c 4 172.16.10.1
PING 172.16.10.1 (172.16.10.1) 56(84) bytes of data.
64 bytes from 172.16.10.1: icmp_seq=1 ttl=64 time=1.14 ms
64 bytes from 172.16.10.1: icmp_seq=2 ttl=64 time=1.04 ms
64 bytes from 172.16.10.1: icmp_seq=3 ttl=64 time=1.10 ms
64 bytes from 172.16.10.1: icmp_seq=4 ttl=64 time=1.10 ms

--- 172.16.10.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3004ms
rtt min/avg/max/mdev = 1.047/1.100/1.145/0.053 ms
```
Аналогичный вывод данных команд выведен в playbook, чтобы не делать это вручную и убедиться, что стенд и RAS работает:)
































# Подготовка тестового стенда
1. Нам необходимо поднять три виртуальных сервера со следующей адресацией и топологией.
![alt text](/screenshots/hw21-1.PNG?raw=true "Screenshot1")  
![alt text](/screenshots/hw21-2.PNG?raw=true "Screenshot2")  

2. Запуск стенда
```
git clone https://github.com/Dmitriy-Iv/Otus-Pr-linux-HW21.git
cd Otus-Pr-linux-HW21/Ansible/
vagrant up
```

# Настроить OSPF между машинами на базе Quagga
1. Настройка OSPF осуществляется на основе FRR. Установка данного пакета, конфигурирование серверов осуществляется с помощью ansible, согласно инструкциям, описанным в методичке. После запуска стенда у нас разворачиваются три виртуальных серверах, с сетевой конфигурацией показанной на схеме. 

2. Проверяем, что демон OSPF поднялся и работает корректно, все наши роутеры обменялись информацией о своих сетях. Проверку будем проводить с frr роутеров через `vtysh`.
 - Заходим на router1 и проверяем что у нас попало в таблицу маршрутизции OSPF. 
```
vagrant@router1:~$ sudo -i
root@router1:~# vtysh

Hello, this is FRRouting (version 8.2.2).
Copyright 1996-2005 Kunihiro Ishiguro, et al.

router1# sh ip ospf route
============ OSPF network routing table ============
N    10.0.10.0/30          [100] area: 0.0.0.0
                           directly attached to enp0s8
N    10.0.11.0/30          [200] area: 0.0.0.0
                           via 10.0.10.2, enp0s8
                           via 10.0.12.2, enp0s9
N    10.0.12.0/30          [100] area: 0.0.0.0
                           directly attached to enp0s9
N    192.168.10.0/24       [100] area: 0.0.0.0
                           directly attached to enp0s10
N    192.168.20.0/24       [200] area: 0.0.0.0
                           via 10.0.10.2, enp0s8
N    192.168.30.0/24       [200] area: 0.0.0.0
                           via 10.0.12.2, enp0s9

============ OSPF router routing table =============

============ OSPF external routing table ===========

```
- Пробуем сделать ping и traceroute до сети 192.168.30.0/24, которая передаётся в OSPF с router3.
```
router1# ping 192.168.30.1
PING 192.168.30.1 (192.168.30.1) 56(84) bytes of data.
64 bytes from 192.168.30.1: icmp_seq=1 ttl=64 time=0.477 ms
64 bytes from 192.168.30.1: icmp_seq=2 ttl=64 time=0.642 ms
^C
--- 192.168.30.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1032ms
rtt min/avg/max/mdev = 0.477/0.559/0.642/0.082 ms

router1# traceroute 192.168.30.1
traceroute to 192.168.30.1 (192.168.30.1), 30 hops max, 60 byte packets
 1  192.168.30.1 (192.168.30.1)  0.725 ms  0.644 ms  0.590 ms
```
- OSPF работает, маршрутизация настраивается с помощью OSPF. Теперь проверим, как изменится маршрут до этой же сети, если мы отключим прямой линк между router1 и router3.
Для этого на router1 отключим интерфейс enp0s9 (либо enp0s9 на router3) и посмотрим ещё раз traceroute.
```
router1# sh int br
Interface       Status  VRF             Addresses
---------       ------  ---             ---------
enp0s3          up      default         10.0.2.15/24
enp0s8          up      default         10.0.10.1/30
enp0s9          up      default         10.0.12.1/30
enp0s10         up      default         192.168.10.1/24
enp0s16         up      default         192.168.56.10/24
lo              up      default

router1# conf t
router1(config)# int enp0s9
router1(config-if)# shutdown
router1(config-if)# do traceroute 192.168.30.1
traceroute to 192.168.30.1 (192.168.30.1), 30 hops max, 60 byte packets
 1  10.0.10.2 (10.0.10.2)  0.576 ms  0.452 ms  0.370 ms
 2  192.168.30.1 (192.168.30.1)  0.911 ms  0.868 ms  0.822 ms
router1(config-if)# no shutdown
```
- Из вывода traceroute можно сделать вывод - что OSPF отработал и трафик пошёл через второй маршрут, через router2, что и требовалось проверить.

# Настройка ассиметричного роутинга
1. Для проверки асимметричного роутинга, нам необходимо сделать стоимость интерфейса enp0s8 на router1 - 1000. Таким образом, маршруты до других роутеров, в том числе router2 - будут менее приоритетными через данный интерфейс (чем меньше cost - тем приоритетней маршрут). Для того, чтобы внести изменения, необходимые для проверки этого типа роутинга, необходимо в файле `main.yml` изменить значение переменной `symmetric_routing` с `empty` на `false`. После этого запустим наш playbook, но с указание только тега - `setup_ospf`.
Также в конфигурацию серверов были внесены изменения, чтобы разрешить ассиметричный трафик - `sysctl net.ipv4.conf.all.rp_filter=0`.
```
dima@Test-Ubuntu-1:~/otus/my-hw21/Ansible$ ansible-playbook provision.yml -t setup_ospf

router1# show ip route ospf
Codes: K - kernel route, C - connected, S - static, R - RIP,
       O - OSPF, I - IS-IS, B - BGP, E - EIGRP, N - NHRP,
       T - Table, v - VNC, V - VNC-Direct, A - Babel, F - PBR,
       f - OpenFabric,
       > - selected route, * - FIB route, q - queued, r - rejected, b - backup
       t - trapped, o - offload failure

O   10.0.10.0/30 [110/300] via 10.0.12.2, enp0s9, weight 1, 00:19:04
O>* 10.0.11.0/30 [110/200] via 10.0.12.2, enp0s9, weight 1, 00:19:09
O   10.0.12.0/30 [110/100] is directly connected, enp0s9, weight 1, 00:19:09
O   192.168.10.0/24 [110/100] is directly connected, enp0s10, weight 1, 00:19:44
O>* 192.168.20.0/24 [110/300] via 10.0.12.2, enp0s9, weight 1, 00:19:04
O>* 192.168.30.0/24 [110/200] via 10.0.12.2, enp0s9, weight 1, 00:19:09

router2# sh ip route ospf
Codes: K - kernel route, C - connected, S - static, R - RIP,
       O - OSPF, I - IS-IS, B - BGP, E - EIGRP, N - NHRP,
       T - Table, v - VNC, V - VNC-Direct, A - Babel, F - PBR,
       f - OpenFabric,
       > - selected route, * - FIB route, q - queued, r - rejected, b - backup
       t - trapped, o - offload failure

O   10.0.10.0/30 [110/100] is directly connected, enp0s8, weight 1, 00:20:34
O   10.0.11.0/30 [110/100] is directly connected, enp0s9, weight 1, 00:20:34
O>* 10.0.12.0/30 [110/200] via 10.0.10.1, enp0s8, weight 1, 00:19:59
  *                        via 10.0.11.1, enp0s9, weight 1, 00:19:59
O>* 192.168.10.0/24 [110/200] via 10.0.10.1, enp0s8, weight 1, 00:19:59
O   192.168.20.0/24 [110/100] is directly connected, enp0s10, weight 1, 00:20:34
O>* 192.168.30.0/24 [110/200] via 10.0.11.1, enp0s9, weight 1, 00:19:59
```
2. После изменения конфигурации на router1 проверяем, как ходят пакеты, зарустив ping.
```
root@router1:~# ping -I 192.168.10.1 192.168.20.1
PING 192.168.20.1 (192.168.20.1) from 192.168.10.1 : 56(84) bytes of data.
64 bytes from 192.168.20.1: icmp_seq=1 ttl=64 time=1.03 ms
64 bytes from 192.168.20.1: icmp_seq=2 ttl=64 time=1.09 ms
.....................
```
3. На router2 запускаем tcpdump, который будет смотреть трафик только на порту enp0s9.
```
root@router2:~# tcpdump -i enp0s9
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on enp0s9, link-type EN10MB (Ethernet), capture size 262144 bytes
19:48:08.369899 ARP, Request who-has router2 tell 10.0.11.1, length 46
19:48:08.369899 IP 192.168.10.1 > router2: ICMP echo request, id 4, seq 16, length 64
19:48:08.369959 ARP, Reply router2 is-at 08:00:27:e4:07:56 (oui Unknown), length 28
19:48:09.371279 IP 192.168.10.1 > router2: ICMP echo request, id 4, seq 17, length 64
19:48:10.372952 IP 192.168.10.1 > router2: ICMP echo request, id 4, seq 18, length 64
19:48:10.495043 IP router2 > ospf-all.mcast.net: OSPFv2, Hello, length 48
19:48:10.495274 IP 10.0.11.1 > ospf-all.mcast.net: OSPFv2, Hello, length 48
19:48:11.374760 IP 192.168.10.1 > router2: ICMP echo request, id 4, seq 19, length 64
19:48:12.376756 IP 192.168.10.1 > router2: ICMP echo request, id 4, seq 20, length 64
19:48:13.378698 IP 192.168.10.1 > router2: ICMP echo request, id 4, seq 21, length 64
^C
10 packets captured
10 packets received by filter
0 packets dropped by kernel
```
4. На router2 запускаем tcpdump, который будет смотреть трафик только на порту enp0s8.
```
root@router2:~# tcpdump -i enp0s8
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on enp0s8, link-type EN10MB (Ethernet), capture size 262144 bytes
19:49:02.523377 IP router2 > 192.168.10.1: ICMP echo reply, id 4, seq 70, length 64
19:49:03.525920 IP router2 > 192.168.10.1: ICMP echo reply, id 4, seq 71, length 64
19:49:04.527641 IP router2 > 192.168.10.1: ICMP echo reply, id 4, seq 72, length 64
19:49:05.529465 IP router2 > 192.168.10.1: ICMP echo reply, id 4, seq 73, length 64
19:49:06.531983 IP router2 > 192.168.10.1: ICMP echo reply, id 4, seq 74, length 64
19:49:07.537088 IP router2 > 192.168.10.1: ICMP echo reply, id 4, seq 75, length 64
19:49:08.539562 IP router2 > 192.168.10.1: ICMP echo reply, id 4, seq 76, length 64
^C
7 packets captured
7 packets received by filter
0 packets dropped by kernel
```
Из вывода видно, что icmp пакет приходит через enp0s9 с router3, а уходит на enp0s8 на router1. Это является ассиметричным роутингом, который по default запрещён.

# Настройка симметичного роутинга
1. Для того, чтобы сделать роутинг симметричным, нам надо выровнять cost на direct-link между router1 и router2. То есть на router2 также на интерфейсе enp0s8 выставить cost 1000. Для этого, необходимо в файле `main.yml` изменить значение переменной `symmetric_routing` с `false` на `true`. После этого запустим наш playbook, но с указание только тега - `setup_ospf`.
```
dima@Test-Ubuntu-1:~/otus/my-hw21/Ansible$ ansible-playbook provision.yml -t setup_ospf

router1# show ip route ospf
Codes: K - kernel route, C - connected, S - static, R - RIP,
       O - OSPF, I - IS-IS, B - BGP, E - EIGRP, N - NHRP,
       T - Table, v - VNC, V - VNC-Direct, A - Babel, F - PBR,
       f - OpenFabric,
       > - selected route, * - FIB route, q - queued, r - rejected, b - backup
       t - trapped, o - offload failure

O   10.0.10.0/30 [110/1000] is directly connected, enp0s8, weight 1, 00:00:13
O>* 10.0.11.0/30 [110/200] via 10.0.12.2, enp0s9, weight 1, 00:00:08
O   10.0.12.0/30 [110/100] is directly connected, enp0s9, weight 1, 00:00:48
O   192.168.10.0/24 [110/100] is directly connected, enp0s10, weight 1, 00:00:48
O>* 192.168.20.0/24 [110/300] via 10.0.12.2, enp0s9, weight 1, 00:00:08
O>* 192.168.30.0/24 [110/200] via 10.0.12.2, enp0s9, weight 1, 00:00:08

router2# sh ip route ospf
Codes: K - kernel route, C - connected, S - static, R - RIP,
       O - OSPF, I - IS-IS, B - BGP, E - EIGRP, N - NHRP,
       T - Table, v - VNC, V - VNC-Direct, A - Babel, F - PBR,
       f - OpenFabric,
       > - selected route, * - FIB route, q - queued, r - rejected, b - backup
       t - trapped, o - offload failure

O   10.0.10.0/30 [110/1000] is directly connected, enp0s8, weight 1, 00:01:03
O   10.0.11.0/30 [110/100] is directly connected, enp0s9, weight 1, 00:01:03
O>* 10.0.12.0/30 [110/200] via 10.0.11.1, enp0s9, weight 1, 00:00:23
O>* 192.168.10.0/24 [110/300] via 10.0.11.1, enp0s9, weight 1, 00:00:23
O   192.168.20.0/24 [110/100] is directly connected, enp0s10, weight 1, 00:01:03
O>* 192.168.30.0/24 [110/200] via 10.0.11.1, enp0s9, weight 1, 00:00:23
```
2. После изменения конфигурации на router2 проверяем, как ходят пакеты, запустив также ping c router1.
```
root@router1:~# ping -I 192.168.10.1 192.168.20.1
PING 192.168.20.1 (192.168.20.1) from 192.168.10.1 : 56(84) bytes of data.
64 bytes from 192.168.20.1: icmp_seq=1 ttl=63 time=1.31 ms
64 bytes from 192.168.20.1: icmp_seq=2 ttl=63 time=0.827 ms
```
3. На router2 запускаем tcpdump, который будет смотреть трафик только на порту enp0s9.
```
root@router2:~# tcpdump -i enp0s9
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on enp0s9, link-type EN10MB (Ethernet), capture size 262144 bytes
20:09:05.603486 IP 192.168.10.1 > router2: ICMP echo request, id 6, seq 6, length 64
20:09:05.603528 IP router2 > 192.168.10.1: ICMP echo reply, id 6, seq 6, length 64
20:09:05.785182 ARP, Request who-has 10.0.11.1 tell router2, length 28
20:09:05.785551 ARP, Reply 10.0.11.1 is-at 08:00:27:4d:f1:f0 (oui Unknown), length 46
20:09:05.787578 ARP, Request who-has router2 tell 10.0.11.1, length 46
20:09:05.787592 ARP, Reply router2 is-at 08:00:27:e4:07:56 (oui Unknown), length 28
20:09:06.605499 IP 192.168.10.1 > router2: ICMP echo request, id 6, seq 7, length 64
20:09:06.605560 IP router2 > 192.168.10.1: ICMP echo reply, id 6, seq 7, length 64
20:09:07.607223 IP 192.168.10.1 > router2: ICMP echo request, id 6, seq 8, length 64
20:09:07.607285 IP router2 > 192.168.10.1: ICMP echo reply, id 6, seq 8, length 64
20:09:08.608896 IP 192.168.10.1 > router2: ICMP echo request, id 6, seq 9, length 64
20:09:08.608968 IP router2 > 192.168.10.1: ICMP echo reply, id 6, seq 9, length 64
20:09:09.609567 IP 192.168.10.1 > router2: ICMP echo request, id 6, seq 10, length 64
20:09:09.609621 IP router2 > 192.168.10.1: ICMP echo reply, id 6, seq 10, length 64
^C
14 packets captured
14 packets received by filter
```
Из данного вывода видно, что теперь пакеты приходят и уходят через один интерфейс enp0s9, то есть трафик между router1 и router2 ходит через router3.