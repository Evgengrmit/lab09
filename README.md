## Laboratory work III

Данная лабораторная работа посвещена изучению систем автоматизации сборки проекта на примере **CMake**

```sh
$ open https://cmake.org/
```

## Tasks

- [ ] 1. Создать публичный репозиторий с названием **lab03** на сервисе **GitHub**
- [ ] 2. Ознакомиться со ссылками учебного материала
- [ ] 3. Выполнить инструкцию учебного материала

## Tutorial
Создание переменных среды и установка их значений
```sh
$ export GITHUB_USERNAME=<имя_пользователя>
```
Начало работы в каталоге workspace
```sh
# Переход в  рабочую директорию
$ cd ${GITHUB_USERNAME}/workspace
$ pushd . # Сохранение текущего каталога в стек
# Cвязывание команды edit с вызовом текстового редактора Atom
$ source scripts/activate
```
Настройка локального репозитория для работы
```sh
# Клонируем предыдущую ЛР2
$ git clone https://github.com/${GITHUB_USERNAME}/lab02.git projects/lab03
$ cd projects/lab03
$ git remote remove  origin # Разрываем прошлую связь с удаленным репозиторием с ЛР2
# Подключение локального репозитория к удаленному репозиторию с ЛР3
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab03.git
```
Ручная компиляция и сборка программ на С++
```sh
# Компилируем обьектный файл из файла print.cpp по стандарту С++20 с указанием,
# где искать заголовочные файлы
$ g++ -std=c++2a -I./include -c sources/print.cpp
$ ls print. # Проверка существования объектного файла
# Вывод информации о бинарном файле, где есть строка print
$ nm print.o | grep print
# Создаем из объектного файла файл статической библиотеки
$ ar rvs print.a print.o
# Смотрим тип созданного архива
$ file print.a
# Компилируем обьектный файл из файла example1.cpp по стандарту С++20 с указанием,
# где искать заголовочные файлы
$ g++ -std=c++2a -I./include -c examples/example1.cpp
$ ls example1.o # Проверка существования объектного файла
# Линковка объектного файла с файлом статической библиотеки в исполняемый файл
$ g++ example1.o print.a -o example1
# Запуск и вывод результата в окно терминала
$ ./example1 && echo
```

```sh
# Компилируем обьектный файл из файла example2.cpp по стандарту С++20 с указанием,
# где искать заголовочные файлы
$ g++ -std=c++2a -I./include -c examples/example2.cpp
# Вывод информации о бинарном файле
$ nm example2.o
# Линковка объектного файла с файлом статической библиотеки в исполняемый файл
$ g++ example2.o print.a -o example2
# Запуск программы и запись результата в файл log.txt
$ ./example2
$ cat log.txt && echo
```
Удаление всех созданных в процессе работы файлов
```sh
$ rm -rf example1.o example2.o print.o
$ rm -rf print.a
$ rm -rf example1 example2
$ rm -rf log.txt
```
Создание файла `CMakeLists.txt` для автоматизированной сборки проекта
```sh
# Устанавливаем минимальную версию CMake и название проекта
$ cat > CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.4)
project(print)
EOF
```

```sh
# Устанавливаем стандарт языка и включаем его
$ cat >> CMakeLists.txt <<EOF

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
EOF
```
Создание статической библиотеки с именем print
```sh
$ cat >> CMakeLists.txt <<EOF

add_library(print STATIC \${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)
EOF
```
Указание, где искать заголовочные файлы
```sh
$ cat >> CMakeLists.txt <<EOF

include_directories(\${CMAKE_CURRENT_SOURCE_DIR}/include)
EOF
```
Создание директории, которую CMake будет использовать в качестве корневого каталога сборки.
```sh
$ cmake -H. -B_build
$ cmake --build _build
```
Создание исполняемых файлов
```sh
$ cat >> CMakeLists.txt <<EOF

add_executable(example1 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example1.cpp)
add_executable(example2 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example2.cpp)
EOF
```
Линковка исполняемых файлов со статической библиотекой
```sh
$ cat >> CMakeLists.txt <<EOF

target_link_libraries(example1 print)
target_link_libraries(example2 print)
EOF
```
Компиляция и сборка проекта с помощью CMake
```sh
$ cmake --build _build
$ cmake --build _build --target print
$ cmake --build _build --target example1
$ cmake --build _build --target example2
```
Проверка работы CMake
```sh
$ ls -la _build/libprint.a
$ _build/example1 && echo
hello
$ _build/example2
$ cat log.txt && echo
hello
$ rm -rf log.txt
```

```sh
$ git clone https://github.com/tp-labs/lab03 tmp
$ mv -f tmp/CMakeLists.txt .
$ rm -rf tmp
```

```sh
$ cat CMakeLists.txt
$ cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
$ cmake --build _build --target install
$ tree _install
```

```sh
$ git add CMakeLists.txt
$ git commit -m"added CMakeLists.txt"
$ git push origin master
```

## Report

```sh
$ popd
$ export LAB_NUMBER=03
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gist REPORT.md
```

## Homework

Представьте, что вы стажер в компании "Formatter Inc.".
### Задание 1
Вам поручили перейти на систему автоматизированной сборки **CMake**.
Исходные файлы находятся в директории [formatter_lib](formatter_lib).
В этой директории находятся файлы для статической библиотеки *formatter*.
Создайте `CMakeList.txt` в директории [formatter_lib](formatter_lib),
с помощью которого можно будет собирать статическую библиотеку *formatter*.

### Задание 2
У компании "Formatter Inc." есть перспективная библиотека,
которая является расширением предыдущей библиотеки. Т.к. вы уже овладели
навыком созданием `CMakeList.txt` для статической библиотеки *formatter*, ваш
руководитель поручает заняться созданием `CMakeList.txt` для библиотеки
*formatter_ex*, которая в свою очередь использует библиотеку *formatter*.

### Задание 3
Конечно же ваша компания предоставляет примеры использования своих библиотек.
Чтобы продемонстрировать как работать с библиотекой *formatter_ex*,
вам необходимо создать два `CMakeList.txt` для двух простых приложений:
* *hello_world*, которое использует библиотеку *formatter_ex*;
* *solver*, приложение которое испольует статические библиотеки *formatter_ex* и *solver_lib*.

**Удачной стажировки!**

## Links
- [Основы сборки проектов на С/C++ при помощи CMake](https://eax.me/cmake/)
- [CMake Tutorial](http://neerc.ifmo.ru/wiki/index.php?title=CMake_Tutorial)
- [C++ Tutorial - make & CMake](https://www.bogotobogo.com/cplusplus/make.php)
- [Autotools](http://www.gnu.org/software/automake/manual/html_node/Autotools-Introduction.html)
- [CMake](https://cgold.readthedocs.io/en/latest/index.html)

```
Copyright (c) 2015-2020 The ISC Authors
```
