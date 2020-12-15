# Linux
## Настройка сети

CentOS
/etc/sysconfig/network-scripts/ifcfg-ens18


Базовые утилиты
nmtui
ip -с link
ip -с addr

Поднять интерфейс.
ifconfig eth0 up
ip link set eth0 up

Назначение IP-адреса или добавление дополнительного к имеющемуся:
ip a add 192.168.0.156/24 dev ens32

Изменение IP-адреса:
ip a change 192.168.0.157/24 dev ens32

Удаление адреса:
ip a del 192.168.163.157/24 dev ens32

Получить адрес у DHCP сервера для всех сетевух
dhclient -v

Получить адрес у DHCP сервера для ens18
dhclient ens18 -v

Освободить DHCP адрес
dhclient ens18 -r


Добавление маршрута по умолчанию:
ip r add default via 192.168.128.254
ip r add default via 172.25.244.3

Добавление статического маршрута:
ip r add 192.168.10.0/24 dev ens160
ip r add 192.168.1.0/24 via 192.168.0.18
ip r add 192.168.10.0/24 via 172.25.244.3
ip r add 172.25.240.0/21 via 172.25.244.1

Удаление маршрутов:
ip r del default via 192.168.160.1
ip r del 192.168.1.0/24 via 192.168.0.18

uuidgen ens160 - узнаем UUID сетевой карты



## DNS

настройки dns
sudo nano /etc/resolv.conf

yum install bind-utils
nslookup

## Репозитории и пакеты

подключение EPEL repo
sudo yum install epel-release

Показать все установленные пакеты
yum list installed
dpkg --get-selections | grep php

## Юзеры

посмотреть список юзеров
sed 's/:.*//' /etc/passwd

список активных юзеров
w
история входов юзеров
last -a
последний вход
lastlog

// Создать пользователя username
useradd username
useradd username -u 1001-m -s /bin/bash
echo "username ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers # важен порядок правил в файле! последующие приорететнее чем предыдущие


// Установить пароль пользователю username
passwd username

// Добавить пользователя username в группу groupname
// список групп тут /etc/group
usermod -a -G groupname username

// Узнать в какой группе пользователь username + UID
id username 

// Изменить оболочку по умолчанию
usermod -s /bin/bash username

Залогиниться от юзера с установленной пустой оболочкой
su www-data -s /bin/bash

=====================
Файлы-папки

скопировать файл
cp file file
скопировать папку
cd -r dir dir

переименовать/переместить файл
mv file file

удалить папку
rm -Rf папка

=====================
Права на файлы

установка владельца
chown vasya:users /путь/имя_каталога_или_файла
рекурсивно на папку
chown -R vasya:users /путь/имя_каталога

установка прав на файл
chmod 755 /путь/имя_каталога_или_файла
на каталог
chmod -R 755 /путь/имя_каталога

Первая цифра означает права для владельца файла/каталога, вторая — для группы, третья — для всех остальных.
Значения цифр:
0 (—) 	— ничего делать нельзя;
1 (—x) 	— выполнение;
2 (-w-) 	— запись;
3 (-wx) 	— запись, выполнение;
4 (r—) 	— чтение;
5 (r-x) 	— чтение, выполнение;
6 (rw-) 	— чтение, запись;
7 (rwx) 	— чтение, запись, выполнение.

Буквами:
    u - владелец файла;
    g - группа файла;
    o - другие пользователи.

chmod ugo+rwx /путь/имя_каталога_или_файла

сделать файл исполняемым
chmod +x script.sh

Установить права только на файлы с помощью find:
find /var/www/node/nodejs.org/ -type f -exec chmod 644 {} \;
Установить права только на папки с помощью find:
find /var/www/node/nodejs.org/ -type d -exec chmod 755 {} \;

=====================
SCP
Копирование с удаленной машины
scp -r ak@172.25.241.250:/home/ak/aws-ssh.sh aws-ssh.sh

Копирование папок или файлов на удаленную машину:
scp -r /home/captain/ root@154.132.6.13:/путь_куда_нужно_скопировать/

Как скопировать все файлы или папки с одного сервера на другой если ты находишься на 3-м сервере:
# scp root@154.132.6.13:/home/test_user/_your_file.tar root@154.132.6.14:/home/test_user_2/

Можно вместо пароля использовать ключ
scp -i "~/keys/my_key" ak@172.25.241.250:/home/ak/aws-ssh.sh aws-ssh.sh
=====================
SSHFS

sudo apt-get install -y sshfs
sudo mkdir /mnt/vps1 && sudo chown -R ubuntu:ubuntu /mnt/vps1

sshfs root@k8-04-1.barm0leykin-do14.devops.rebrain.srwx.net:/ /mnt/vps1 -o uid=1000,gid=1000

размонтировать
fusermount -u /mnt/vps1
=====================
SSH


Разрешить доступо по ssh для root (сначала нужно задать для него пароль, если не установлен)
vi /etc/ssh/sshd_config
 PermitRootLogin yes
systemctl restart sshd

сгенерировать ключи
ssh-keygen

показать публичный ключ
cat ~/.ssh/id_rsa.pub

более модный ключ
ssh-keygen -a 100 -t ed25519 -f ~/.ssh/id_ed25519 -C "$(whoami)@$(hostname)-$(date '+%Y-%m-%d')"


Подключиться используя ключ
ssh -i  "~/.ssh/id_rsa" ubuntu@130.61.197.149

Добавить ключ на удаленный сервер
ssh-copy-id 'ak@172.25.241.240'

Забыть хост
ssh-keygen -f "/home/ubuntu/.ssh/known_hosts" -R 5.189.227.238

Увеличение времени сессии SSH
vi /etc/ssh/sshd_config 

TCPKeepAlive yes
ClientAliveInterval 300
ClientAliveCountMax 60
время сессии = 300*60 в секундах
=====================
Firewall

Открыть порт:
 sudo firewall-cmd --permanent --add-port=1050/tcp
Перезагрузить
 sudo firewall-cmd --reload

 
=====================
Установка графической оболочки
 нужен epel!
 
 Установка GNOME:
yum groupinstall "GNOME Desktop"
Установка MATE: 
yum groupinstall "MATE Desktop"

Установка KDE:
yum groupinstall "KDE Plasma Workspaces"

Установка Cinnamon:
yum --enablerepo=epel -y install cinnamon*

Установка Xfce:
yum groupinstall "Xfce"
--
startx

Делаем загрузку ОС по-умолчанию в графическом режиме:
systemctl set-default graphical.target

Если в дальнейшем будет необходимо вернуться к запуску без GUI, то выполните следующее:
systemctl set-default multi-user.target


=======================
Установить часовой пояс
sudo ln -sf /usr/share/zoneinfo/Europe/Moscow /etc/localtime
синхронизировать часы с хардварным таймером
sudo /sbin/hwclock --systohc

=======================
Создать архив tar.gz
tar -czvf archive.tar.gz /path/to/files

Создать архивы для каждого файла или директории в текущем каталоге
for i in `ls`; do tar -czf $i.tar.gz $i; echo $i; done;
 
Распаковать архив tar.gz
tar -zvxf jenkins2020-06-05.tar.gz
tar -xvzf -C /home/user archive.tar.gz

Распаковать архив gz
gunzip file.log.gz


=======================
SMB / CIFS

sudo yum install cifs-utils

$ smb_share.sh:
mount -t cifs -o credentials=/home/ak/winpassword //172.25.241.150/i$/WORK/_шпаргалки/ ak_doc/

$ cat winpassword
username=ak@rzdp.ru
password=pwd


Поиск
Найти файлы более 5мб
find . -type f -size +5M 2>/dev/null
2>/dev/null используется, чтобы не показывать ошибки (например, если нет доступа к файлу).

Добавим в вывод информацию о каждом файле и отсортируем список по размеру.
find . -mount -type f -size +5M -print 2>/dev/null | xargs -r -d '\n' ls -lh | sort -k5,5 -h -r

Вариант
du -ahx . | sort -rh | head -5

поиск текста внутри файлов в директории
grep -r "mydomain.com" /etc/apache2/


=======================
Локаль

Посмотреть текущую локаль
localectl status

список доступных
localectl list-locales

установить ru_RU.utf8
localectl set-locale ru_RU.utf8



========================
NTP

установить аппаратное время
/sbin/hwclock --systohc --localtime

date
timedatectl status
timedatectl set-timezone Europe/Moscow

стандартная служба systemd
vi /etc/systemd/timesyncd.conf
	[Time]
	NTP=172.25.240.1
systemctl restart systemd-timesyncd

-
chronyd
apt install chrony
systemctl start chronyd
systemctl enable chronyd

========================

## SWAP

//fallocate -l 2G /swapfile
dd if=/dev/zero of=/swapfile bs=1G count=4
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile

========================
Посмотреть размер всех подпапок
cd /
for i in *; do du -hs $i 2> /dev/null;  done;


## Xorg
поднять приоритет xorg чтобы мыша не тормозила
sudo renice -n -5 $(pidof Xorg)

## Vim
:w !sudo tee %