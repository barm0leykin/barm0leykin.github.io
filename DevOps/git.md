# GIT

## Установка
sudo yum install git

посмотреть настройки
> git config -l
git config --global --list

настройка пользователя
> git config --global user.email "you@example.com"
> git config --global user.name "Your Name"

задать текстовый редактор
> git config --global core.editor "nano"


====

Создание пустого репозитория с рабочим каталогом.
> git init

Создание пустого расшареного репозитория без рабочего каталога.
> git init --bare --shared RepName

Удалить репозиторий
> rm -rf .git

Добавить удаленный репозиторий
> git remote add origin <Repository_Location>
> git remote add origin https://github.com/barm0leykin/RiotGalaxy/
Посмотреть удаленный репозиторий
> git remote
> git remote -v

Залить изменения в удаленный репозиторий (все ветки)
> git push origin 

Залить определенную ветку в удаленный репозиторий
> git push origin <branch_name>

Залить upstream в удаленный (новый?) репозиторий
> git push -u origin master

Насильно залить изменения в удаленный репозиторий
> git push -f origin master

Замержить все ветки с удаленного репозитория:
> git pull origin

Аналогично предыдущему, но накатывается только ветка master:
> git pull origin master

Скачать все ветки с origin, но не мержить их в локальный репозиторий:
> git fetch origin

Аналогично предыдущему, но только для одной заданной ветки:
> git fetch origin master

## ветки
Создать ветку и переключиться на неё
git branch hotfix
git checkout hotfix

То же в сокращенной форме
git checkout -b hotfix

Переключиться на определенную ветку в определенном репозитории
> git checkout -B RiotGalaxy.git/master

Удалить ветку
git branch -D hotfix


Просмотр файлов в репозитории
> git ls-tree master

Добавление файлов в stage.
> git add filename
> git add *

Удаление файлов
> git rm filename

Переименование файла
> git mv test_main_file.c test_main.c

Создание коммита.
> git commit -m “message”

Удаление коммита 
git reset HEAD~
Файлы из данного последнего коммита переходят в статус unstaged, то есть в то состояние, в котором они были до коммита

Полное удаление последнего коммита, включая все изменения файлов
git reset --hard HEAD~1

Исправление последнего комимта (не делать если запушен на публичный сервер)
git commit --amend

Просмотр статуса каталога.
> git status

Просмотр коммитов в репозитории.
> git log

Просмотр коммитов в репозитории с сокращенным выводом информации.
> git log --oneline

Просмотр веток в графическом виде
git log --graph --abbrev-commit --decorate --all --oneline
git log --graph --oneline --all

Просмотр трех последних:
git log -n 3 --oneline

с определенной даты
> git log --since="2018-03-05 23:21:00" --oneline

определенного автора
> git log --author="Writer" --oneline

содержит "create"
> git log --grep="create" --oneline

Краткая версия log
git shortlog

просмотр коммитов
> git show cf3d9d8f 
> git show master
> git show HEAD

## Теги
Просмотр меток (тегов) коммитов
git tag

добавить тег v0.1 на коммит 33bed604
git tag -a v0.1 -m "version 0.1" 33bed604baaf045abcab4b938ecddb44a85c8f87

запушить тег
git push --tags


=====
Check out, review, and merge locally

Step 1. Fetch and check out the branch for this merge request
git fetch origin
git checkout -b "dev" "origin/dev"

Step 2. Review the changes locally

Step 3. Merge the branch and fix any conflicts that come up
git fetch origin
git checkout "master"
git merge --no-ff "dev"

Step 4. Push the result of the merge to GitLab
git push origin "master"




================================================================
Установка git на CentOS как посредника между Visual Studio и Github
**************
CentOS

сначала нужно настроить доступ по ssh

установка GIT
> yum install git
> git config --global user.email "you@example.com"
> git config --global user.name "Your Name"

создал каталог /opt/RiotGalaxy
дал права для юзера на запись

создал пустой репозиторий
> git init --bare --shared

**************
Visual Studio

добавил репозиторий на CentOS (можно в настройках VS в параметрах репозитория)
> git remote add origin ssh://ak@172.25.241.250:/opt/git/RiotGalaxy/

**************
CentOS

добавил удаленный репозиторий
> git remote add origin https://github.com/barm0leykin/RiotGalaxy
залил туда ветку master, в которой уже есть файлы с VS
>  git push -f origin master


Залить ветку с репозитория на CentOS на Github:
> cd /opt/git/RiotGalaxy/
> git push origin master
====================================================================

Стащить приложение с сервера в текущий (.) каталог
git clone https://github.com/maler1988/cookies.git .

с указанием ssh ключа
git clone git@gitlab.rebrainme.com:docker_users_repos/106/dkr19-registry.git --config core.sshCommand="ssh -i ~/.ssh/cloud"



===================================================================
Создаю репозиторий в папке с проектом и заливаю его на github

Инициализация - создастся скрытая папка .git
git init
Добавляем ориждин - удаленный репозиторий
git remote add origin https://github.com/barm0leykin/you_project
Добавление в коммит текущего каталога (можно git add --all тогда добавится всё с поддерикториями)
git add .
Коммит с описанием
git commit -m "my first commit"
Пушим всё на удаленный репозиторий
git push origin master
