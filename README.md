## Laboratory work VI

<a href="https://yandex.ru/efir/?stream_id=vGWlFO3of-Rg"><img src="https://raw.githubusercontent.com/tp-labs/lab06/master/preview.png" width="640"/></a>

Данная лабораторная работа посвещена изучению средств пакетирования на примере **CPack**

```sh
$ open https://cmake.org/Wiki/CMake:CPackPackageGenerators
```

## Tasks


- [x] 1. Создать публичный репозиторий с названием **lab06** на сервисе **GitHub**
- [x] 2. Выполнить инструкцию учебного материала
- [x] 3. Ознакомиться со ссылками учебного материала
- [x] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial
Создание переменных среды и установка их значений, а также связывание команд с их "новыми" названиями
```sh
$ export GITHUB_USERNAME=Evgengrmit
$ export GITHUB_EMAIL==evgengrmit@icloud.com
$ alias edit=atom
$ alias gsed=sed # for *-nix system
```
Начало работы в каталоге workspace
```sh
# Переход в  рабочую директорию
$ cd ${GITHUB_USERNAME}/workspace
$ pushd . # Сохранение текущего каталога в стек
# Активация Node.js и rvm
$ source scripts/activate
```
Настройка git-репозитория **lab06** для работы
```sh
$ git clone https://github.com/${GITHUB_USERNAME}/lab05 projects/lab06
$ cd projects/lab06
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab06
```
Настройка **CPack** через определение CPack-переменных внутри файла `CMakeLists.txt`
Добавление истории версий для проекта и установка версию 0.1.0.0
```sh
$ gsed -i "" '/project(print)/a\
set(PRINT_VERSION_STRING "v\${PRINT_VERSION}")
' CMakeLists.txt
$ gsed -i "" '/project(print)/a\
set(PRINT_VERSION\
  \${PRINT_VERSION_MAJOR}.\${PRINT_VERSION_MINOR}.\${PRINT_VERSION_PATCH}.\${PRINT_VERSION_TWEAK})
' CMakeLists.txt
$ gsed -i "" '/project(print)/a\
set(PRINT_VERSION_TWEAK 0)
' CMakeLists.txt
$ gsed -i "" '/project(print)/a\
set(PRINT_VERSION_PATCH 0)
' CMakeLists.txt
$ gsed -i "" '/project(print)/a\
set(PRINT_VERSION_MINOR 1)
' CMakeLists.txt
$ gsed -i "" '/project(print)/a\
set(PRINT_VERSION_MAJOR 0)
' CMakeLists.txt
# Проверка изменений
$ git diff
```
Создание файлов `DESCRIPTION`(описание пакета) и `ChangeLog.md` (описание изменений в пакете)
```sh
$ touch DESCRIPTION && edit DESCRIPTION
$ touch ChangeLog.md
$ export DATE="`LANG=en_US date +'%a %b %d %Y'`"
$ cat > ChangeLog.md <<EOF
* ${DATE} ${GITHUB_USERNAME} <${GITHUB_EMAIL}> 0.1.0.0
- Initial RPM release
EOF
```
Создание и заполнение файла `CPackConfig.cmake`
```sh
# Подключение необходимых системных библиотек
$ cat > CPackConfig.cmake <<EOF
include(InstallRequiredSystemLibraries)
EOF
```
Установка значений переменных в пакете
```sh
$ cat >> CPackConfig.cmake <<EOF
set(CPACK_PACKAGE_CONTACT ${GITHUB_EMAIL})
set(CPACK_PACKAGE_VERSION_MAJOR \${PRINT_VERSION_MAJOR})
set(CPACK_PACKAGE_VERSION_MINOR \${PRINT_VERSION_MINOR})
set(CPACK_PACKAGE_VERSION_PATCH \${PRINT_VERSION_PATCH})
set(CPACK_PACKAGE_VERSION_TWEAK \${PRINT_VERSION_TWEAK})
set(CPACK_PACKAGE_VERSION \${PRINT_VERSION})
set(CPACK_PACKAGE_DESCRIPTION_FILE \${CMAKE_CURRENT_SOURCE_DIR}/DESCRIPTION)
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "static C++ library for printing")
EOF
```
Добавление файлов в пакет
```sh
$ cat >> CPackConfig.cmake <<EOF

set(CPACK_RESOURCE_FILE_LICENSE \${CMAKE_CURRENT_SOURCE_DIR}/LICENSE)
set(CPACK_RESOURCE_FILE_README \${CMAKE_CURRENT_SOURCE_DIR}/README.md)
EOF
```
Настройки для RPM-пакета
```sh
$ cat >> CPackConfig.cmake <<EOF

set(CPACK_RPM_PACKAGE_NAME "print-devel")
set(CPACK_RPM_PACKAGE_LICENSE "MIT")
set(CPACK_RPM_PACKAGE_GROUP "print")
set(CPACK_RPM_CHANGELOG_FILE \${CMAKE_CURRENT_SOURCE_DIR}/ChangeLog.md)
set(CPACK_RPM_PACKAGE_RELEASE 1)
EOF
```
Настройки для Debian-пакета
```sh
$ cat >> CPackConfig.cmake <<EOF

set(CPACK_DEBIAN_PACKAGE_NAME "libprint-dev")
set(CPACK_DEBIAN_PACKAGE_PREDEPENDS "cmake >= 3.0")
set(CPACK_DEBIAN_PACKAGE_RELEASE 1)
EOF
```
Подключение модуля CPack
```sh
$ cat >> CPackConfig.cmake <<EOF

include(CPack)
EOF
```
Добавление `CPackConfig.cmake` в основной `CMakeLists.txt`
```sh
$ cat >> CMakeLists.txt <<EOF

include(CPackConfig.cmake)
EOF
```

```sh
$ gsed -i 's/lab05/lab06/g' README.md
```
Добавляем созданные файлы в репозиторий, делаем коммит, добавляем тэг с версией, загружаем на удаленный репозиторий уже с тэгом
```sh
$ git add .
$ git commit -m"added cpack config"
[master 6c3facf] added cpack config
 5 files changed, 226 insertions(+), 197 deletions(-)
 create mode 100644 CPackConfig.cmake
 create mode 100644 ChangeLog.md
 create mode 100644 DESCRIPTION
 rewrite README.md (77%)
$ git tag v0.1.0.0
$ git push origin master --tags
Enumerating objects: 10, done.
Counting objects: 100% (10/10), done.
Delta compression using up to 12 threads
Compressing objects: 100% (7/7), done.
Writing objects: 100% (7/7), 3.08 KiB | 3.08 MiB/s, done.
Total 7 (delta 2), reused 0 (delta 0)
remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
To https://github.com/Evgengrmit/lab06
   7d9576e..6c3facf  master -> master
 * [new tag]         v0.1.0.0 -> v0.1.0.0
```
Авторизация в `travis-ci`
```sh
$ travis login --auto
Successfully logged in as Evgengrmit!
$ travis enable
Evgengrmit/lab06: enabled :)
```
Сборка и генерация пакета (1 способ через `CPack`)
```sh
$ cmake -H. -B_build
-- The C compiler identification is AppleClang 11.0.3.11030032
...
-- Configuring done
-- Generating done
-- Build files have been written to: /Users/evgengrmit/Evgengrmit/workspace/projects/lab06/_build
$ cmake --build _build
[ 50%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[100%] Linking CXX static library libprint.a
[100%] Built target print
$ cd _build
$ cpack -G "TGZ"
CPack: Create package using TGZ
CPack: Install projects
CPack: - Run preinstall target for: print
CPack: - Install project: print []
CPack: Create package
CPack: - package: /Users/evgengrmit/Evgengrmit/workspace/projects/lab06/_build/print-0.1.0.0-Darwin.tar.gz generated.
$ cd ..
```
Сборка и генерация пакета (2 способ через `CMake`)
```sh
$ cmake -H. -B_build -DCPACK_GENERATOR="TGZ"
-- Configuring done
-- Generating done
-- Build files have been written to: /Users/evgengrmit/Evgengrmit/workspace/projects/lab06/_build
$ cmake --build _build --target package
Run CPack packaging tool...
CPack: Create package using TGZ
CPack: Install projects
CPack: - Run preinstall target for: print
CPack: - Install project: print []
CPack: Create package
CPack: - package: /Users/evgengrmit/Evgengrmit/workspace/projects/lab06/_build/print-0.1.0.0-Darwin.tar.gz generated.
```
Перемещение в `artifacts`
```sh
$ mkdir artifacts
$ mv _build/*.tar.gz artifacts
$ tree artifacts
```

## Report

Создание отчета по ЛР № 6
```sh
$ popd
$ export LAB_NUMBER=06
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gist REPORT.md
```

## Links

- [DMG](https://cmake.org/cmake/help/latest/module/CPackDMG.html)
- [DEB](https://cmake.org/cmake/help/latest/module/CPackDeb.html)
- [RPM](https://cmake.org/cmake/help/latest/module/CPackRPM.html)
- [NSIS](https://cmake.org/cmake/help/latest/module/CPackNSIS.html)

```
Copyright (c) 2015-2020 The ISC Authors
```
