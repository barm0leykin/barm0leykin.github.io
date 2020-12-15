# DOCKER
## Полезные ссылки
Шпаргалка по командам:
https://vidikon.info/commands-docker/

## УСТАНОВКА
установка
curl -fsSL https://get.docker.com/ | sh
или на ubuntu
wget -qO- https://get.docker.com/ | sh

Добавить своего пользователя в группу docker
sudo usermod -aG docker $(whoami)
После этого перелогиниться, а то права не применятся

добавление сервиса в автозагрузку
systemctl enable docker

запуск
sudo systemctl start docker
перезапуск
sudo service docker restart

проверка работы
sudo systemctl status docker


## Настройка

История комманд
history | grep docker
история сборки
docker history baseimage-ub20:lates

Версия:
docker version

docker info

## ОБРАЗА

Список образов:
docker images

Информация об образе
docker image inspect myimage

Создать образ из контейнера:
docker commit my-container myimage

создать с измененной точкой входа
docker commit --change='ENTRYPOINT ["python3"]' create-image-from-me


Создать образ с использованием Dockerfile (если он в текущем каталоге то явно его не указываем)
docker build -t image-from-dockerfile .

Создать образ с использованием Dockerfile -f указывает путь к Dockerfile
docker build -t image-from-dockerfile -f /path/to/a/Dockerfile .

Коммит состояния контейнера в новый образ
docker commit -m "What did you do to the image" -a "Author Name" container-id repository/new_image_name

Удаление образов по шаблону
docker ps -a |  grep "шаблон"
docker images | grep "шаблон" | awk '{print $1}' | xargs docker rm

Удаление всех образов
docker rmi $(docker images -a -q)

Удаление ненужных образов
docker rmi $(docker images --filter "dangling=true" -q --no-trunc)


## КОНТЕЙНЕРЫ

docker ps - Список запущенных контейнеров.
docker ps -a -Список всех контейнеров
docker logs - Показывает стандартный вывод контейнера.
docker stop - Останавливает запущенные контейнеры.
docker start

получить информацию о контейнере
sudo docker inspect [container ID]

Запуск контейнера:
docker run --rm -it ubuntu
Ключи:
-it 	//интерактивный режим
-d 		//режим демона
-m 64m 	//ограничить использование памяти 65 мб
--rm 	//удаление после выполнения
--restart=always 	//автоматический перезапуск контейнера

docker start -i ubuntu

#### SSH hint
Проброс сокета SSH, чтобы изнутри контейнера можно было ходить наружу с теми же правами что и в хостовой машине
```
docker run -it --network=host -v $(dirname $SSH_AUTH_SOCK):$(dirname $SSH_AUTH_SOCK) -e SSH_AUTH_SOCK=$SSH_AUTH_SOCK --name ub20 ub20
```

Удаление всех остановленных контейнеров
docker rm $(docker ps -a -f status=exited -q)

Удалить все контейнеры кроме тех, который оканчиваются на '_data':
docker rm $(docker ps -a | grep -v "_data" | awk 'NR>1 {print $1}')

Остановка и удаление контейнеров
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)

## ФАЙЛЫ

Примонтирование текущей директории (результат команды pwd) в директорию /home контейнера:
docker run -it --rm -v $(pwd):/home ubuntu
или
docker run --rm -it -v /home:/home ubuntu:14.04

Создать том 
docker volume create my-vol

Список томов:
docker volume ls

Удалить том:
docker volume rm my-vol

Скопировать файл из контейнера
docker cp [ID контейнера]:/usr/local/apache2/conf/httpd.conf ./httpd.conf

Docker никогда не удаляет data volumes, даже если контейнеры, которые их создали, удалены.

Для того чтобы посмотреть список осиротевших томов, используйте команду:
docker volume ls -qf dangling=true

Для удаления таких томов:
docker volume rm $(docker volume ls -qf dangling=true)


## ВЗАИМОДЕЙСТВИЕ

Для того чтобы пробросить порты контейнера при запуске необходимо использовать ключ -p:
docker run -d --name port-export -p <port_on_host_machine>:<port_inside_container> image

пример:
docker run -d --name stepik-ports -p 81:80 parseq/stepik-ports-docker
curl --request GET localhost:81

Для подключения к уже запущенному контейнеру удобно использовать команду:
docker exec -it <container-name> bash


Список сетей докера:
docker network ls

сети:
none - без сети
host - использовать сетевые устройства хоста
bridge - (по умолчанию) проброс сети

Создать свою сеть
docker network create custom

информация о сети 
docker network inspect custom | more

подключить сеть в контейнер
docker run -d --name one --network=custom ubuntu

## ЛОГИ

docker logs mycontainer
docker logs mtproto-proxy

##  Dockerfile
-
FROM ubuntu:15.04 //образ на базе которого создается новый
LABEL maintainer = "Создатель образа"	
COPY . /app
RUN make /app
CMD python /app/app.py
-

ADD: копирует файл с хоста в контейнер.
CMD: задаёт команды по умолчанию, которые нужно выполнить, или передает параметру ENTRYPOINT.
ENTRYPOINT: устанавливает стандартную точку входа приложения внутри контейнера.
ENV: задаёт переменные окружения (например, ключ-значение).
EXPOSE: открывает порт.
FROM: задаёт базовый образ.
MAINTAINER: указывает имя автора/владельца данного Dockerfile.
RUN: запускает команду и сохраняет комментарий для образа.
USER: указывает пользователя, от имени которого будут запускаться последующие комманды.
VOLUME: монтирует каталог с хоста в контейнер.
WORKDIR: указывает каталог, в котором должны запускаться директивы CMD.



===Скрипт для тестов===
touch test.sh
chmod +x test.sh
nano test.sh

--
#!/bin/bash
docker image rm dockerfile-extended
docker build -t dockerfile-extended .
docker run -it --rm dockerfile-extended
--
./test.sh -запуск
===
#!/bin/bash
docker image rm dockerfile-extended
docker build -t dockerfile-extended .
echo ==stop
docker stop dock2
echo ==rm
docker rm dock2
echo ==run
docker run -d -it --name dock2 dockerfile-extended
echo ==exec
docker exec -it dock2 /bin/bash
===

===Загрузка образов Docker в репозиторий===
логин
docker login -u docker-registry-username

Пуш образа в репозиторий
docker push docker-registry-username/docker-image-name







======================================================================
Тестовый полигон Docker с Zabbix и Jenkins
http://nyukers.blogspot.com/2018/03/docker-zabbix-jenkins.html#axzz5eitY4W51

docker search zabbix
docker pull zabbix


Поднимаем Мysql-server
docker run --name mysql-server -t \
      -e MYSQL_DATABASE="zabbix" \
      -e MYSQL_USER="zabbix" \
      -e MYSQL_PASSWORD="zabbix" \
      -e MYSQL_ROOT_PASSWORD="root" \
      -d mysql:5.7
	  
Проверим что MySQL поднялся
docker logs mysql-server
В конце должно быть
>Starting MySQL или Ready for connections.


Поднимаем zabbix-server
docker run --name zabbix-server -t -p 10051:10051 \
-e DB_SERVER_HOST="172.17.0.2" -e MYSQL_DATABASE="zabbix" \
-e MYSQL_USER="zabbix" -e MYSQL_PASSWORD="zabbix" \
-e MYSQL_ROOT_PASSWORD="root" \
--link mysql-server:mysql \
-d zabbix/zabbix-server-mysql:latest

Проверяем логи:
>docker logs zabbix-server

Проверка доступности MySQL из контейнера Zabbix-server
>docker exec -it zabbix-server bash
bash> mysql -u zabbix -p zabbix -h 172.17.0.2
bash> mysqladmin ping -u zabbix -p zabbix -h 172.17.0.2
Использование параметра --link не обязательно.


Поднимаем zabbix-web (frontend zabbix)
docker run --name zabbix-web -t -p 80:80 \
-e DB_SERVER_HOST="172.17.0.2" \
-e MYSQL_USER="zabbix" \
-e MYSQL_PASSWORD="zabbix" \
-e ZBX_SERVER_HOST="172.17.0.3" \
-e ZBX_SERVER_NAME="MyZabbix" \
-e PHP_TZ="Europe/Riga" \
-d zabbix/zabbix-web-apache-mysql:latest

Проверяем логи:
docker logs zabbix-web

Первый вход в frontend zabbix:
http://IP_адрес_хоста
admin/zabbix


Настроить zabbix на мониторинг localhost

Для мониторинга самого хоста, его ЦП и памяти ставим zabbix-agent на Centos:
rpm -Uvh https://repo.zabbix.com/zabbix/3.2/rhel/7/x86_64/zabbix-release-3.2-1.el7.noarch.rpm
yum update
yum install zabbix-agent

Говорим zabbix-агенту где его сервер:
nano /etc/zabbix/zabbix_agentd.conf
Server=172.17.0.3
ServerActive=172.17.0.3
Hostname=Zabbix server iGov

Запускаем агент и добавляем его запуск в автозагрузку хоста:
systemctl start zabbix-agent
systemctl enable zabbix-agent

Проверка лога:
cat /var/log/zabbix/zabbix_agentd.log
и портов на прослушивание:
netstat -napl | grep zabbix

