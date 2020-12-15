
куда можно зайти:
staging-virginia-jenkins

пример железного хоста:
staging-frontend-beta
там можно зайти в контейнер
docker exec -i -t frontend-django-staging-frontend-beta /bin/bash


смонтированый том в контейнерах:
/var/log/container/
tail /var/log/container/stdout.log

он же на хосте
/var/log/container/$containername
/var/log/container/frontend-django-staging-frontend-beta


scp staging-frontend-beta:/var/log/container/frontend-django-staging-frontend-beta/gostatsd .


####  все сервисы контейнера
/etc/service/
