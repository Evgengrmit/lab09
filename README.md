## Laboratory work IX
[![Build Status](https://travis-ci.com/Evgengrmit/lab09.svg?branch=master)](https://travis-ci.com/Evgengrmit/lab09)

<a href="https://yandex.ru/efir/?stream_id=vYrKRcFKi46o"><img src="https://raw.githubusercontent.com/tp-labs/lab09/master/preview.png" width="640"/></a>

Данная лабораторная работа посвещена изучению процесса создания артефактов на примере **Github Release**

```sh
$ open https://help.github.com/articles/creating-releases/
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab09** на сервисе **GitHub**
- [x] 2. Ознакомиться со ссылками учебного материала
- [x] 3. Получить токен для доступа к репозиториям сервиса **GitHub**
- [x] 4. Выполнить инструкцию учебного материала
- [x] 5. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial
Создание переменных среды и установка их значений.
```sh
$ export GITHUB_TOKEN=**********************
$ export GITHUB_USERNAME=Evgengrmit
$ export PACKAGE_MANAGER=brew
$ export GPG_PACKAGE_NAME=gpg
```
Установка утилиты **xclip**, которая представляет доступ к буферу обмена X из командной строки.
```sh
# for *-nix system
$ $PACKAGE_MANAGER install xclip
$ alias gsed=sed
# Связывание команды pbcopy с записью данных в секцию clipboard
$ alias pbcopy='xclip -selection clipboard'
# Связывание команды pbpaste с чтением данных из секции clipboard
$ alias pbpaste='xclip -selection clipboard -o'
```
Установка приложения командной строки **github-release**, для работы с релизами на **Github**.
```sh
# Переход в  рабочую директорию
$ cd ${GITHUB_USERNAME}/workspace
$ pushd . # Сохранение текущего каталога в стек
# Активация Node.js и rvm
$ source scripts/activate
# Скачивание и установка пакета, написанного на языке программирования Go
$ go get github.com/aktau/github-release
```
Настройка git-репозитория **lab09** для работы.
```sh
$ git clone https://github.com/${GITHUB_USERNAME}/lab08 projects/lab09
$ cd projects/lab09
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab09
```
Замена всех упоминаний `lab08` на `lab09`.
```sh
$ gsed -i "" 's/lab08/lab09/g' README.md
```
Установка и работа с программой **GPG** для шифрования информации и создания электронных цифровых подписей.
```sh
# Установка программы
$ $PACKAGE_MANAGER install ${GPG_PACKAGE_NAME}
# Список всех сгенерированных ключей в длинном формате
$ gpg --list-secret-keys --keyid-format
gpg: создан каталог '/Users/evgengrmit/.gnupg'
gpg: создан щит с ключами '/Users/evgengrmit/.gnupg/pubring.kbx'
gpg: /Users/evgengrmit/.gnupg/trustdb.gpg: создана таблица доверия
# Генерация полноценной пары ключей для создания релизов
$ gpg --full-generate-key
открытый и секретный ключи созданы и подписаны.

pub   rsa4096 2020-05-08 [SC]
      02A40AF6288ECC99D486C4AFD7C1F3269EC77E39
uid                      Evgenij Grigorev (Creating the first release on Github) <evgengrmit@icloud.com>
sub   rsa4096 2020-05-08 [E]
$ gpg --list-secret-keys --keyid-format LONG
evgengrmit@MBPEvgeGrigorev lab09 % gpg --list-secret-keys --keyid-format LONG
gpg: проверка таблицы доверия
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: глубина: 0  достоверных:   1  подписанных:   0  доверие: 0-, 0q, 0n, 0m, 0f, 1u
/Users/evgengrmit/.gnupg/pubring.kbx
------------------------------------
sec   rsa4096/D7C1F3269EC77E39 2020-05-08 [SC]
      -----------------------------------------
uid               [  абсолютно ] Evgenij Grigorev (Creating the first release on Github) <evgengrmit@icloud.com>
ssb   rsa4096/E9284E5876D10536 2020-05-08 [E]
# Вывести список приватных ключей для пользователя Evgengrmit
$ gpg -K ${GITHUB_USERNAME}
# Создание переменной окружения с сохраненным в ней публичным ключом
$ GPG_KEY_ID=$(gpg --list-secret-keys --keyid-format LONG | grep ssb | tail -1 | awk '{print $2}' | awk -F'/' '{print $2}')
# Создание переменной окружения с сохраненным в ней секретным ключом
$ GPG_SEC_KEY_ID=$(gpg --list-secret-keys --keyid-format LONG | grep sec | tail -1 | awk '{print $2}' | awk -F'/' '{print $2}')
# Вывести ключ в ASCII и копировать его в буфер
$ gpg --armor --export ${GPG_KEY_ID} | pbcopy
# Вставить из буфера
$ pbpaste
-----BEGIN PGP PUBLIC KEY BLOCK-----
...
-----END PGP PUBLIC KEY BLOCK-----
$ open https://github.com/settings/keys
# Добавление секретного ключа к настройкам Git'a
$ git config user.signingkey ${GPG_SEC_KEY_ID}
$ git config gpg.program gpg
```
Создание скрипта дя добавления сообщения к тегу
```sh
$ test -r ~/.zsh_profile && echo 'export GPG_TTY=$(tty)' >> ~/.zsh_profile
$ echo 'export GPG_TTY=$(tty)' >> ~/.profile
```
Генерация пакета с расширением `.tar.gz`
```sh
$ cmake -H. -B_build -DCPACK_GENERATOR="TGZ"
$ cmake --build _build --target package
Scanning dependencies of target print
[ 25%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[ 50%] Linking CXX static library libprint.a
[ 50%] Built target print
Scanning dependencies of target demo
[ 75%] Building CXX object CMakeFiles/demo.dir/demo/main.cpp.o
[100%] Linking CXX executable demo
[100%] Built target demo
Run CPack packaging tool...
CPack: Create package using TGZ
CPack: Install projects
CPack: - Run preinstall target for: print
CPack: - Install project: print []
CPack: Create package
CPack: - package: /Users/evgengrmit/Evgengrmit/workspace/projects/lab09/_build/print-0.1.0.0-Darwin.tar.gz generated.
```
Инициализация проекта на **Travis CI**
```sh
$ travis login --auto --pro
Successfully logged in as Evgengrmit!
$ travis enable --pro
Evgengrmit/lab09: enabled :)
```
Создание тега для коммита
```sh
# Создание тега с сообщением с информацией
$ git tag -s v0.12.0.0
$ git tag -v v0.1.0.0
object e5d026df3e4f32f4f4848c24bba2310db5073a49
type commit
tag v0.1.0.0
tagger Evgengrmit <evgengrmit@gmail.com> 1588968443 +0300

Create first release on Github
gpg: Подпись сделана пятница,  8 мая 2020 г. 23:08:00 MSK
gpg:                ключом RSA с идентификатором 02A40AF6288ECC99D486C4AFD7C1F3269EC77E39
gpg: Действительная подпись пользователя "Evgenij Grigorev (Creating the first release on Github) <evgengrmit@icloud.com>" [абсолютное]
$ git show v0.1.0.0
tag v0.1.0.0
Tagger: Evgengrmit <evgengrmit@gmail.com>
Date:   Fri May 8 23:07:23 2020 +0300

Create first release on Github
-----BEGIN PGP SIGNATURE-----
...
-----END PGP SIGNATURE-----

commit e5d026df3e4f32f4f4848c24bba2310db5073a49 (HEAD -> master, tag: v0.1.0.0)
Author: Evgengrmit <evgengrmit@gmail.com>
$ git push origin master --tags
Enumerating objects: 322, done.
Counting objects: 100% (322/322), done.
Delta compression using up to 12 threads
Compressing objects: 100% (136/136), done.
Writing objects: 100% (322/322), 7.10 MiB | 1.27 MiB/s, done.
Total 322 (delta 172), reused 321 (delta 172)
remote: Resolving deltas: 100% (172/172), done.
To https://github.com/Evgengrmit/lab09
 * [new branch]      master -> master
 * [new tag]         v0.1.0.0 -> v0.1.0.0
```
Создание релиза
```sh
# Проверка версии приложения
$ github-release --version
github-release v0.8.1
# Вывод информации об тегах и релизах пользователя Evgengrmit в репозитории lab09
$ github-release info -u ${GITHUB_USERNAME} -r lab09
tags:
- v0.1.0.0 (commit: https://api.github.com/repos/Evgengrmit/lab09/commits/e5d026df3e4f32f4f4848c24bba2310db5073a49)
releases:
# Создание релиза
$ github-release release \
    --user ${GITHUB_USERNAME} \
    --repo lab09 \
    --tag v0.1.0.0 \
    --name "libprint" \
    --description "my first release"
```
Добавление артефакта с указанием ОС и архитектуры, на которых происходила компиляция библиотеки
```sh
$ export PACKAGE_OS=`uname -s` PACKAGE_ARCH=`uname -m`
$ export PACKAGE_FILENAME=print-${PACKAGE_OS}-${PACKAGE_ARCH}.tar.gz
$ github-release upload \
    --user ${GITHUB_USERNAME} \
    --repo lab09 \
    --tag v0.1.0.0 \
    --name "${PACKAGE_FILENAME}" \
    --file _build/*.tar.gz
```

```sh
$ github-release info -u ${GITHUB_USERNAME} -r lab09tags:
- v0.1.0.0 (commit: https://api.github.com/repos/Evgengrmit/lab09/commits/e5d026df3e4f32f4f4848c24bba2310db5073a49)
releases:
- v0.1.0.0, name: 'libprint', description: 'my first release', id: 26329050, tagged: 08/05/2020 at 20:07, published: 08/05/2020 at 20:45, draft: ✗, prerelease: ✗
  - artifact: print-Darwin-x86_64.tar.gz, downloads: 0, state: uploaded, type: application/octet-stream, size: 20 kB, id: 20559779
# Скачивание артефакта из раздела релизов для проверки
$ wget https://github.com/${GITHUB_USERNAME}/lab09/releases/download/v0.1.0.0/${PACKAGE_FILENAME}
--2020-05-08 23:51:56--  https://github.com/Evgengrmit/lab09/releases/download/v0.1.0.0/print-Darwin-x86_64.tar.gz
...
Сохранение в: «print-Darwin-x86_64.tar.gz»
print-Darwin-x86_64 100%[===================>]  19,44K   122KB/s    за 0,2s    
2020-05-08 23:51:57 (122 KB/s) - «print-Darwin-x86_64.tar.gz» сохранён [19909/19909]
# Просмотр содержимого архива
$ tar -ztf ${PACKAGE_FILENAME}
print-0.1.0.0-Darwin/cmake/
print-0.1.0.0-Darwin/cmake/print-config-noconfig.cmake
print-0.1.0.0-Darwin/cmake/print-config.cmake
print-0.1.0.0-Darwin/bin/
print-0.1.0.0-Darwin/bin/demo
print-0.1.0.0-Darwin/include/
print-0.1.0.0-Darwin/include/print.hpp
print-0.1.0.0-Darwin/lib/
print-0.1.0.0-Darwin/lib/libprint.a
```

## Report
Создание отчета по ЛР № 9
```sh
$ popd
$ export LAB_NUMBER=09
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gistup -m "lab${LAB_NUMBER}"
```

## Links

- [Create Release](https://help.github.com/articles/creating-releases/)
- [Get GitHub Token](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)
- [Signing Commits](https://help.github.com/articles/signing-commits-with-gpg/)
- [Go Setup](http://www.golangbootcamp.com/book/get_setup)
- [github-release](https://github.com/aktau/github-release)

```
Copyright (c) 2015-2020 The ISC Authors
```
