## Laboratory work VII
[![Build Status](https://travis-ci.com/Evgengrmit/lab07.svg?branch=master)](https://travis-ci.com/Evgengrmit/lab07)
[![Build status](https://ci.appveyor.com/api/projects/status/dtc14dr12tn3xo6m?svg=true)](https://ci.appveyor.com/project/Evgengrmit/lab07)

<a href="https://yandex.ru/efir/?stream_id=vDHLoKtKoa3o"><img src="https://raw.githubusercontent.com/tp-labs/lab07/master/preview.png" width="640"/></a>

Данная лабораторная работа посвещена изучению систем управления пакетами на примере **Hunter**

```sh
$ open https://github.com/ruslo/hunter
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab07** на сервисе **GitHub**
- [x] 2. Выполнить инструкцию учебного материала
- [x] 3. Ознакомиться со ссылками учебного материала
- [x] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial
Создание переменных среды и установка их значений, а также связывание команд с их "новыми" названиями.
```sh
$ export GITHUB_USERNAME=Evgengrmit
$ alias gsed=sed
```
Начало работы в каталоге workspace
```sh
# Переход в  рабочую директорию
$ cd ${GITHUB_USERNAME}/workspace
$ pushd . # Сохранение текущего каталога в стек
# Активация Node.js и rvm
$ source scripts/activate
```
Настройка git-репозитория **lab07** для работы
```sh
$ git clone https://github.com/${GITHUB_USERNAME}/lab06 projects/lab07
$ cd projects/lab07
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab07
```
Скачивание и подключение модуля `HunterGate`
```sh
$ mkdir -p cmake # Создание директории где будут храниться файлы Hunter
# Скачивание данных из файла в удаленном репозитории и их запись в файл HunterGate.cmake
$ wget https://raw.githubusercontent.com/cpp-pm/gate/master/cmake/HunterGate.cmake -O cmake/HunterGate.cmake
--2020-05-06 13:57:06--  https://raw.githubusercontent.com/cpp-pm/gate/master/cmake/HunterGate.cmake
Распознаётся raw.githubusercontent.com (raw.githubusercontent.com)… 151.101.112.133
Подключение к raw.githubusercontent.com (raw.githubusercontent.com)|151.101.112.133|:443... соединение установлено.
HTTP-запрос отправлен. Ожидание ответа… 200 OK
Длина: 17070 (17K) [text/plain]
Сохранение в: «cmake/HunterGate.cmake»

cmake/HunterGate.cm 100%[===================>]  16,67K  --.-KB/s    за 0,04s   

2020-05-06 13:57:06 (437 KB/s) - «cmake/HunterGate.cmake» сохранён [17070/17070]
# Добавление HunterGate к CMake
$ gsed -i "" '/cmake_minimum_required(VERSION 3.4)/a\

include("cmake/HunterGate.cmake")
HunterGate(
    URL "https:\//github.com/cpp-pm/hunter/archive/v0.23.251.tar.gz"
    SHA1 "5659b15dc0884d4b03dbd95710e6a1fa0fc3258d"
)
' CMakeLists.txt
```
Теперь не нужно скачивать **GTest** самостоятельно. **Hunter** сам подтянет добавленные с помощью функции `hunter_add_package`.
```sh
# Удаление подмодуля с GTest
$ git rm -rf third-party/gtest
rm 'third-party/gtest'
# Добавение через hunter пакета gtest и его поиск
$ gsed -i "" '/set(PRINT_VERSION_STRING "v\${PRINT_VERSION}")/a\

hunter_add_package(GTest)
find_package(GTest CONFIG REQUIRED)
' CMakeLists.txt
# Удаление строки с добавлением поддиректории gtest
$ gsed -i "" 's/add_subdirectory(third-party/gtest)//' CMakeLists.txt
# Замена обращение к gtest gtest_main на GTest::gtest_main
$ gsed -i "" 's/gtest_main/GTest::gtest_main/' CMakeLists.txt
```
Сборка прокта при помощи **Hunter**.
```sh
# Видим как полключаются пакеты при помощи Hanter'a
$ cmake -H. -B_builds -DBUILD_TESTS=ON
...
-- [hunter] Calculating Toolchain-SHA1
-- [hunter] Calculating Config-SHA1
-- [hunter] HUNTER_ROOT: /Users/evgengrmit/.hunter
-- [hunter] [ Hunter-ID: 5659b15 | Toolchain-ID: a56efe5 | Config-ID: 65a58ee ]
-- [hunter] GTEST_ROOT: /Users/evgengrmit/.hunter/_Base/5659b15/a56efe5/65a58ee/Install (ver.: 1.10.0)
...
-- Build files have been written to: /Users/evgengrmit/Evgengrmit/workspace/projects/lab07/_builds
$ cmake --build
Scanning dependencies of target print
...
[100%] Built target check
$ cmake --build _builds --target test
Running tests...
Test project /Users/evgengrmit/Evgengrmit/workspace/projects/lab07/_builds
    Start 1: check
1/1 Test #1: check ............................   Passed    0.02 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.02 sec
# Вывод файлов из директории .hunter
$ ls -la $HOME/.hunter
total 0
drwxr-xr-x   3 evgengrmit  staff    96  1 апр 19:55 .
drwxr-xr-x+ 35 evgengrmit  staff  1120  6 май 15:23 ..
drwxr-xr-x   7 evgengrmit  staff   224  6 май 16:18 _Base
```
Установка **Hunter** в систему
```sh
$ git clone https://github.com/cpp-pm/hunter $HOME/Evgengrmit/workspace/projects/hunter
$ export HUNTER_ROOT=$HOME/Evgengrmit/workspace/projects/hunter
$ rm -rf _builds
$ cmake -H. -B_builds -DBUILD_TESTS=ON
-- The C compiler identification is AppleClang 11.0.3.11030032
...
-- [hunter] Build step successful (dir: /Users/evgengrmit/Evgengrmit/workspace/projects/hunter/_Base/xxxxxxx/a56efe5/b97958b/Build/GTest)
-- [hunter] Cache saved: /Users/evgengrmit/Evgengrmit/workspace/projects/hunter/_Base/Cache/raw/f7299bccf7e9fb0a24ab88ded5f793d31340c3c7.tar.bz2
...
-- Build files have been written to: /Users/evgengrmit/Evgengrmit/workspace/projects/lab07/_builds
$ cmake --build _builds
Scanning dependencies of target print
...
[100%] Linking CXX executable check
[100%] Built target check
$ cmake --build _builds --target test
Running tests...
Test project /Users/evgengrmit/Evgengrmit/workspace/projects/lab07/_builds
    Start 1: check
1/1 Test #1: check ............................   Passed    0.00 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.01 sec
```
Добавление конфигурационного файла в проект, который будет содержать необходимую версию GTest.
```sh
# Просмотр дефолтной версии GTest
$ cat $HUNTER_ROOT/cmake/configs/default.cmake | grep GTest
hunter_default_version(GTest VERSION 1.7.0-hunter-6)
hunter_default_version(GTest VERSION 1.10.0)
# Просмотр всех версий GTest, поддерживаемых Hunter'ом
$ cat $HUNTER_ROOT/cmake/projects/GTest/hunter.cmake
$ mkdir cmake/Hunter
# Установка нужной версии GTest
$ cat > cmake/Hunter/config.cmake <<EOF
hunter_config(GTest VERSION 1.7.0-hunter-9)
EOF
# add LOCAL in HunterGate function
```
Добавление файла, использующего библиотеку `print`
```sh
$ mkdir demo
#
$ cat > demo/main.cpp <<EOF
#include <print.hpp>

#include <cstdlib>

int main(int argc, char* argv[])
{
  const char* log_path = std::getenv("LOG_PATH");
  if (log_path == nullptr)
  {
    std::cerr << "undefined environment variable: LOG_PATH" << std::endl;
    return 1;
  }
  std::string text;
  while (std::cin >> text)
  {
    std::ofstream out{log_path, std::ios_base::app};
    print(text, out);
    out << std::endl;
  }
}
EOF

$ gsed -i '/endif()/a\

add_executable(demo ${CMAKE_CURRENT_SOURCE_DIR}/demo/main.cpp)
target_link_libraries(demo print)
install(TARGETS demo RUNTIME DESTINATION bin)
' CMakeLists.txt
```
Добавление подмодуля **polly**, который содержит инструкции для сборки проектов с установленным **Hunter**.
```sh
$ mkdir tools
$ git submodule add https://github.com/ruslo/polly tools/polly
$ tools/polly/bin/polly.py --test
Python version: 3.7
...
-
SUCCESS
$ tools/polly/bin/polly.py --install
...
Generate: 0:00:02.613114s
Build: 0:00:01.123806s
-
Total: 0:00:03.737309s
-
SUCCESS
# Сборка проекта на компиляторе со станартом с++14
$ tools/polly/bin/polly.py --toolchain clang-cxx14
...
Generate: 0:00:22.544218s
Build: 0:00:02.083481s
-
Total: 0:00:24.628108s
-
SUCCESS
```

## Report
Создание отчета по ЛР № 7
```sh
$ popd
$ export LAB_NUMBER=07
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gist REPORT.md
```

## Links

- [Create Hunter package](https://docs.hunter.sh/en/latest/creating-new/create.html)
- [Custom Hunter config](https://github.com/ruslo/hunter/wiki/example.custom.config.id)
- [Polly](https://github.com/ruslo/polly)

```
Copyright (c) 2015-2020 The ISC Authors
```
