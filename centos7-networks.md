# Настройка сети в CentOS / RedHat
Шпаргалка для типовых действий при настройке сети в CentOS

### Перезапуск сети для применения настроек
```bash
/etc/init.d/network restart
или
systemctl restart network-online.target
```
### Сетевые утилиты (ifconfig и тд)
Теперь ifconfig по умолчанию не устанавливается, вместо неё в дистрибъютив входит утилита ip
```bash
yum install net-tools
yum install bind-utils
```
## Просмотр информации

### Состояние интерфейсов
```bash
ip -с link
```
### ip адреса
```bash
ip -с addr
ifconfig
```
### netstat
```bash
netstat -nr
netstat -tulp
netstat -tulpan
```
## Настройка параметров сети налету
Внимание! После перезагрузки эти настройки сохранены не будут!

### Поднять/потушить интерфейс.
```bash
ifconfig eth0 up
ip link set eth0 up
```
### Назначение IP-адреса или добавление дополнительного к имеющемуся на интерфейсе:
```bash
ip a add 192.168.0.156/24 dev ens32

### Изменение IP-адреса:
```bash
ip a change 192.168.0.157/24 dev ens32
```
### Удаление адреса:
```bash
ip a del 192.168.163.157/24 dev ens32
```
### Получить адрес у DHCP сервера для всех сетевух
```bash
dhclient -v
```
### Получить адрес у DHCP сервера для ens18
```bash
dhclient ens18 -v
```
### Освободить DHCP адрес
```bash
dhclient ens18 -r
```
### Добавление маршрута по умолчанию:
```bash
ip r add default via 172.25.244.3
```
### Добавление статического маршрута:
```bash
ip r add 172.25.240.0/21 via 172.25.244.1
```
### Удаление маршрутов:
```bash
ip r del default via 192.168.160.1
ip r del 192.168.1.0/24 via 192.168.0.18
```

## Настройка постоянных параметров

### Утилита nmtui
Сетевые параметры можно задать с помощью псевдографической утилиты nmtui в интерактивном реджиме, должна быть предустановлена

### Настройка hostname
```bash
cat /etc/sysconfig/network
HOSTNAME=gw.test
```
### настройки dns
sudo nano /etc/resolv.conf

### Настройка постоянных маршрутов 
```bash
vi /etc/sysconfig/network-scripts/route-ens18
172.25.240.0/21 via 172.25.244.1 dev ens18 metric 99
```
Если вдруг такого файла нет, значит не установлен пакет network-scripts
```bash
yum install network-scripts
```
### Настройка интерфейсов
Пример настройки интерфейса со статическим адресом
```bash
vi /etc/sysconfig/network-scripts/ifcfg-ens18
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static	# У этого интерфейса статический адрес
IPADDR=172.25.244.3
NETMASK=255.255.255.128
GATEWAY=172.25.244.1
DNS=172.25.244.10
DEFROUTE=no
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens18
UUID=ab3263da-2ca7-47be-a5ca-823f97911a52
DEVICE=ens18
ONBOOT=yes
#METRIC=xyz	# можно добавить метрику сюда
```
Пример настройки интерфейса с динамическим адресом
```bash
vi /etc/sysconfig/network-scripts/ifcfg-ens19
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=dhcp		# Этот интерфейс получает адрес по DHCP
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens19
UUID=03ae3455-e579-45bb-9c63-5066ac6e01c0
DEVICE=ens19
ONBOOT=yes
NM_CONTROLLED=no - отключить NetworkManager
```
###  При необходимости узнаем UUID сетевой карты
```bash
uuidgen ens160 
```
