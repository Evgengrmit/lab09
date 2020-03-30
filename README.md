## Laboratory work II

Данная лабораторная работа посвещена изучению систем контроля версий на примере **Git**.

```bash
$ open https://git-scm.com
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab02** и с лиценцией **MIT**
- [x] 2. Сгенирировать токен для доступа к сервису **GitHub** с правами **repo**
- [x] 3. Ознакомиться со ссылками учебного материала
- [x] 4. Выполнить инструкцию учебного материала

## Tutorial
Создание переменных среды и установка их значений
```sh
#Создание переменных среды и установка их значений
$ export GITHUB_USERNAME=Evgengrmit
$ export GITHUB_EMAIL=evgengrmit@gmail.com
$ export GITHUB_TOKEN=*******************************
#Cвязывание команды edit с вызовом текстового редактора Atom
$ alias edit=atom
```
Начало работы в каталоге `workspace`
```sh
# Переход в  рабочую директорию
$ cd ${GITHUB_USERNAME}/workspace
# Выполнение скрипта добавляющего Node.js в PATH
$ source scripts/activate
```
Создание директории `~/.config` с настройками конфигурации Git
```sh
$ mkdir ~/.config
#Создание файла hub с настройками конфигурации Git
$ cat > ~/.config/hub <<EOF
github.com:
- user: ${GITHUB_USERNAME}
  oauth_token: ${GITHUB_TOKEN}
  protocol: https
EOF
# Установка конфигурации работы по протоколу  https утилиты hub  
$ git config --global hub.protocol https
```

```sh
$ mkdir projects/lab02 && cd projects/lab02
# Создание локального git-репозитория (подведение существующего каталога под версионный контроль)
$ git init
Initialized empty Git repository in /Users/evgengrmit/Evgengrmit/workspace/projects/lab02/.git/
# Указание пользовательских настроек: адреса почты , имени пользователя
$ git config --global user.name ${GITHUB_USERNAME}
$ git config --global user.email ${GITHUB_EMAIL}
# Проверка настроек
$ git config -e --global
# # Подключение локального репозитория к удаленному серверу
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab02.git
# Объединение изменений, присутствующих в удаленном репозитории, в локальный рабочий каталог
$ git pull origin master
remote: Enumerating objects: 6, done.
remote: Counting objects: 100% (6/6), done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 6 (delta 0), reused 3 (delta 0), pack-reused 0
Unpacking objects: 100% (6/6), done.
From https://github.com/Evgengrmit/lab02
 * branch            master     -> FETCH_HEAD
 * [new branch]      master     -> origin/master
 # Создание пустого файла README.md
$ touch README.md
# Cписок изменений в каталоге
$ git status
#  Добавление файла README.md в отслеживаемые
$ git add README.md
# Фиксация изменений в локальном репозитории (коммит)
$ git commit -m"added README.md"
# Помещение изменений в главную ветку удаленного хранилища, связанного с рабочим каталогом
$ git push origin master
```

Добавить на сервисе **GitHub** в репозитории **lab02** файл **.gitignore**
со следующем содержимом:

```sh
*build*/
*install*/
*.swp
.idea/
```
Работа с локальным репозиторием
```sh
# Объединение изменений, присутствующих в удаленном репозитории, в локальный рабочий каталог
$ git pull origin master
remote: Enumerating objects: 4, done.
remote: Counting objects: 100% (4/4), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.
From https://github.com/Evgengrmit/lab02
 * branch            master     -> FETCH_HEAD
   be4941d..3ff8bc4  master     -> origin/master
Updating be4941d..3ff8bc4
Fast-forward
 .gitignore | 4 ++++
 1 file changed, 4 insertions(+)
 create mode 100644 .gitignore
# Cписок коммитов
$ git log
commit 3ff8bc4b93b52e26176a1ea0a9a63314c621290d (HEAD -> master, origin/master)
Author: Evgengrmit <54779142+Evgengrmit@users.noreply.github.com>
Date:   Mon Mar 30 13:15:55 2020 +0300
    Create .gitignore

commit be4941d64750919141ddf9fab0c557d1b61dc8ad
Author: Evgengrmit <evgengrmit@gmail.com>
Date:   Sun Mar 29 23:46:20 2020 +0300
    added README.md

commit 79ab77866bcbbab0a382dd00fc8cfb70808febe5
Author: Evgengrmit <54779142+Evgengrmit@users.noreply.github.com>
Date:   Sun Mar 29 22:39:53 2020 +0300
    Initial commit
```
Работа в локальном репозитории - написание программы на C++
```sh
$ mkdir sources
$ mkdir include
$ mkdir examples
$ cat > sources/print.cpp <<EOF
#include <print.hpp>

void print(const std::string& text, std::ostream& out)
{
  out << text;
}

void print(const std::string& text, std::ofstream& out)
{
  out << text;
}
EOF
```

```sh
$ cat > include/print.hpp <<EOF
#include <fstream>
#include <iostream>
#include <string>

void print(const std::string& text, std::ofstream& out);
void print(const std::string& text, std::ostream& out = std::cout);
EOF
```

```sh
$ cat > examples/example1.cpp <<EOF
#include <print.hpp>

int main(int argc, char** argv)
{
  print("hello");
}
EOF
```

```sh
$ cat > examples/example2.cpp <<EOF
#include <print.hpp>

#include <fstream>

int main(int argc, char** argv)
{
  std::ofstream file("log.txt");
  print(std::string("hello"), file);
}
EOF
```
Заполнение файла README.md
```sh
$ edit README.md
```
Публикация новых файлов на удаленном сервере
```sh
$ git status
$ git add .
$ git commit -m"added sources"
$ git push origin master
```

## Report
Создание отчета по лабораторной работе
```sh
$ cd ~/workspace/
$ export LAB_NUMBER=02
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER}.git tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md # Создание отчета
$ gist REPORT.md # Публикация отчета
```

## Links

- [hub](https://hub.github.com/)
- [GitHub](https://github.com)
- [Bitbucket](https://bitbucket.org)
- [Gitlab](https://about.gitlab.com)
- [LearnGitBranching](http://learngitbranching.js.org/)

```
Copyright (c) 2015-2020 The ISC Authors
```
