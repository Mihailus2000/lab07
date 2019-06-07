## Laboratory work VI

Данная лабораторная работа посвещена изучению средств пакетирования на примере **CPack**

```ShellSession
$ open https://cmake.org/Wiki/CMake:CPackPackageGenerators
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab06** на сервисе **GitHub**
- [x] 2. Выполнить инструкцию учебного материала
- [x] 3. Ознакомиться со ссылками учебного материала
- [x] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

Подготовка окружения
```ShellSession
$ export GITHUB_USERNAME=Mihailus2000             # Добавление переменной окружения
$ export GITHUB_EMAIL=Mihail14112000@mail.ru      # Добавление переменной окружения    
$ alias edit=nano                                 # Создание синонима команды вызова редактора nano
$ alias gsed=sed # for *-nix system               Создание синонима команды gsed
```
Подготовка к новой ЛР и запуск скрипта
```ShellSession
$ cd ${GITHUB_USERNAME}/workspace                 # Переход в указанную директорию
$ pushd .                                         # Сохранение текущей

~/Mihailus2000/workspace ~/Mihailus2000/workspace

$ source scripts/activate                         # Запус скрипта активации
```
Скачка файлов прошлой ЛР
```ShellSession
$ git clone https://github.com/${GITHUB_USERNAME}/lab06 projects/lab06     # Клонироание файлов из удалённого репозитория

Клонирование в «projects/lab06»…
remote: Enumerating objects: 40, done.
remote: Counting objects: 100% (40/40), done.
remote: Compressing objects: 100% (23/23), done.
remote: Total 40 (delta 11), reused 40 (delta 11), pack-reused 0
Распаковка объектов: 100% (40/40), готово.

$ cd projects/lab06                               # Переход в указанную директорию
$ git remote remove origin                        # Удаление ссылки на старый репозиторий
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab06       # Добавление ссылки на новый репозиторий
```

Редактирование CMakeList.txt
```ShellSession
$ gsed -i '/project(print)/a\                             # Запись в конец файла указанных строк
set(PRINT_VERSION_STRING "v\${PRINT_VERSION}")
' CMakeLists.txt
$ gsed -i '/project(print)/a\                             # Запись в конец файла указанных строк
set(PRINT_VERSION\
  \${PRINT_VERSION_MAJOR}.\${PRINT_VERSION_MINOR}.\${PRINT_VERSION_PATCH}.\${PRINT_VERSION_TWEAK})
' CMakeLists.txt
$ gsed -i '/project(print)/a\                             # Запись в конец файла указанных строк
set(PRINT_VERSION_TWEAK 0)
' CMakeLists.txt
$ gsed -i '/project(print)/a\                             # Запись в конец файла указанных строк
set(PRINT_VERSION_PATCH 0)
' CMakeLists.txt
$ gsed -i '/project(print)/a\                             # Запись в конец файла указанных строк
set(PRINT_VERSION_MINOR 1)
' CMakeLists.txt
$ gsed -i '/project(print)/a\                             # Запись в конец файла указанных строк
set(PRINT_VERSION_MAJOR 0)
' CMakeLists.txt
$ git diff                                                # Выводит отличия между коммитами

diff --git a/CMakeLists.txt b/CMakeLists.txt
index 05cc72b..e62bf3f 100644
--- a/CMakeLists.txt
+++ b/CMakeLists.txt
@@ -6,6 +6,13 @@ set(CMAKE_CXX_STANDARD_REQUIRED ON)
 option(BUILD_EXAMPLES "Build examples" OFF)
 
 project(print)
+set(PRINT_VERSION_MAJOR 0)
+set(PRINT_VERSION_MINOR 1)
+set(PRINT_VERSION_PATCH 0)
+set(PRINT_VERSION_TWEAK 0)
+set(PRINT_VERSION
+  ${PRINT_VERSION_MAJOR}.${PRINT_VERSION_MINOR}.${PRINT_VERSION_PATCH}.${PRINT_VERSION_TWEAK})
+set(PRINT_VERSION_STRING "v${PRINT_VERSION}")
 
 add_library(print STATIC ${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)
 

```
Добавление необходимых файлов для создания RPM пакета
```ShellSession
$ touch DESCRIPTION && edit DESCRIPTION                 # Создание и редактрование файла DESCRIPTION
$ touch ChangeLog.md                                    # Создание файла ChangeLog.md
$ export DATE="`LANG=en_US date +'%a %b %d %Y'`"        # Создание переменной окружения DATE
$ cat > ChangeLog.md <<EOF                              # Запись указанных строк в файл 
* ${DATE} ${GITHUB_USERNAME} <${GITHUB_EMAIL}> 0.1.0.0
- Initial RPM release
EOF
```
Добавление отдельного cmake файла с конфигом 
```ShellSession       
$ cat > CPackConfig.cmake <<EOF                         # Запись текста в файл
include(InstallRequiredSystemLibraries)
EOF
```
Добавлени пакетов в файл конфигурации
```ShellSession
$ cat >> CPackConfig.cmake <<EOF                        # Запись текста в файл     
set(CPACK_PACKAGE_CONTACT ${GITHUB_EMAIL})
set(CPACK_PACKAGE_VERSION_MAJOR \${PRINT_VERSION_MAJOR})
set(CPACK_PACKAGE_VERSION_MINOR \${PRINT_VERSION_MINOR})
set(CPACK_PACKAGE_VERSION_PATCH \${PRINT_VERSION_PATCH})
set(CPACK_PACKAGE_VERSION_TWEAK \${PRINT_VERSION_TWEAK})
set(CPACK_PACKAGE_VERSION \${PRINT_VERSION})
set(CPACK_PACKAGE_DESCRIPTION_FILE \${CMAKE_CURRENT_SOURCE_DIR}/DESCRIPTION)
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "static c++ library for printing")
EOF
```

Дополнение конфигурации с путями к файлам лицензии и README.md
```ShellSession
$ cat >> CPackConfig.cmake <<EOF                        # Запись текста в файл    

set(CPACK_RESOURCE_FILE_LICENSE \${CMAKE_CURRENT_SOURCE_DIR}/LICENSE)
set(CPACK_RESOURCE_FILE_README \${CMAKE_CURRENT_SOURCE_DIR}/README.md)
EOF
```

Добавление в конфигурацию информации о RPM пакете
```ShellSession
$ cat >> CPackConfig.cmake <<EOF                        # Запись текста в файл    

set(CPACK_RPM_PACKAGE_NAME "print-devel")
set(CPACK_RPM_PACKAGE_LICENSE "MIT")
set(CPACK_RPM_PACKAGE_GROUP "print")
set(CPACK_RPM_CHANGELOG_FILE \${CMAKE_CURRENT_SOURCE_DIR}/ChangeLog.md)
set(CPACK_RPM_PACKAGE_RELEASE 1)
EOF
```
Добавление информации о DEB
```ShellSession
$ cat >> CPackConfig.cmake <<EOF                       # Запись текста в файл      

set(CPACK_DEBIAN_PACKAGE_NAME "libprint-dev")
set(CPACK_DEBIAN_PACKAGE_PREDEPENDS "cmake >= 3.0")
set(CPACK_DEBIAN_PACKAGE_RELEASE 1)
EOF
```
Подключение CPack в CMake конфиге
```ShellSession
$ cat >> CPackConfig.cmake <<EOF                       # Запись текста в файл    

include(CPack)
EOF
```
Подключение в собранной конфигурации в CMakeLists.txt 
```ShellSession
$ cat >> CMakeLists.txt <<EOF                           # Запись текста в файл    

include(CPackConfig.cmake)
EOF
```
Изменение README.md
```ShellSession
$ gsed -i 's/lab06/lab06/g' README.md                   # Замена lab06 на lab06
```

Фиксация и отправка изменений на репозиторий
```ShellSession
$ git add .                                             # Фиксация всех изменений
$ git commit -m"added cpack config"                     # Коммит зафиксированных изменений

master 6287a2b] added cpack config
 4 files changed, 32 insertions(+)
 create mode 100644 CPackConfig.cmake
 create mode 100644 ChangeLog.md
 create mode 100644 DESCRIPTION

$ git tag v0.1.0.0                                      # Установка тэга
$ git push origin master --tags                         # Отправка изменений с определённым тегом

Перечисление объектов: 8, готово.
Подсчет объектов: 100% (8/8), готово.
При сжатии изменений используется до 4 потоков
Сжатие объектов: 100% (5/5), готово.
Запись объектов: 100% (6/6), 995 bytes | 995.00 KiB/s, готово.
Всего 6 (изменения 2), повторно использовано 0 (изменения 0)
remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
To https://github.com/Mihailus2000/lab06
   86b62aa..6287a2b  master -> master
 * [new tag]         v0.1.0.0 -> v0.1.0.0

```

Авторизация и активация репозитория в Travis CI
```ShellSession
$ travis login --auto         # Авторизация

Successfully logged in as Mihailus2000!

$ travis enable               # Активация

Detected repository as Mihailus2000/lab06, is this correct? |yes| yes
Mihailus2000/lab06: enabled :)

```
Сборка и компиляция через CMake. Создание пакета через CPack
```ShellSession
$ cmake -H. -B_build      # Сборка. В директорию B_build

-- The C compiler identification is GNU 8.2.0
-- The CXX compiler identification is GNU 8.2.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /home/mihailus/Mihailus2000/workspace/projects/lab06/_build

$ cmake --build _build      # Компиляция в директории _build

[ 50%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[100%] Linking CXX static library libprint.a
[100%] Built target print

$ cd _build                 # Переход в указанную директорию
$ cpack -G "TGZ"            # Упаковка файлов с использованием формата "TGZ"

CPack: Create package using TGZ
CPack: Install projects
CPack: - Run preinstall target for: print
CPack: - Install project: print
CPack: Create package
CPack: - package: /home/mihailus/Mihailus2000/workspace/projects/lab06/_build/print-0.1.0.0-Linux.tar.gz generated.

$ cd ..                     # Переход на уровень выше
```

Сборка и создание пакета через CMake
```ShellSession
$ cmake -H. -B_build -DCPACK_GENERATOR="TGZ"      # Сборка

-- Configuring done
-- Generating done
-- Build files have been written to: /home/mihailus/Mihailus2000/workspace/projects/lab06/_build

$ cmake --build _build --target package           # Компиляция цели package

[100%] Built target print
Run CPack packaging tool...
CPack: Create package using TGZ
CPack: Install projects
CPack: - Run preinstall target for: print
CPack: - Install project: print
CPack: Create package
CPack: - package: /home/mihailus/Mihailus2000/workspace/projects/lab06/_build/print-0.1.0.0-Linux.tar.gz generated.

```
Перемещение собранного пакета и отображение в виде дерева
```ShellSession
$ mkdir artifacts                                 # Создание директории
$ mv _build/*.tar.gz artifacts                    # Перемещение проекта
$ tree artifacts                                  # Отображение дерева указанной папки

artifacts
└── print-0.1.0.0-Linux.tar.gz

0 directories, 1 file

```

## Report

```ShellSession
$ popd
$ export LAB_NUMBER=06
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gistup -m "lab${LAB_NUMBER}"
```

## Homework

После того, как вы настроили взаимодействие с системой непрерывной интеграции,</br>
обеспечив автоматическую сборку и тестирование ваших изменений, стоит задуматься</br>
о создание пакетов для измениний, которые помечаются тэгами (см. вкладку [releases](https://github.com/tp-labs/lab06/releases)).</br>
Пакет должен содержать приложение _solver_ из [предыдущего задания](https://github.com/tp-labs/lab03#задание-1)
Таким образом, каждый новый релиз будет состоять из следующих компонентов:
- архивы с файлами исходного кода (`.tar.gz`, `.zip`)
- пакеты с бинарным файлом _solver_ (`.deb`, `.rpm`, `.msi`, `.dmg`)

В качестве подсказки:
```bash
$ cat .travis.yml
os: osx
script:
...
- cpack -G DragNDrop # dmg

$ cat .travis.yml
os: linux
script:
...
- cpack -G DEB # deb

$ cat .travis.yml
os: linux
addons:
  apt:
    packages:
    - rpm
script:
...
- cpack -G RPM # rpm

$ cat appveyor.yml
platform:
- x86
- x64
build_script:
...
- cpack -G WIX # msi
```

Для этого нужно добавить ветвление в конфигурационные файлы для **CI** со следующей логикой:</br>
если **commit** помечен тэгом, то необходимо собрать пакеты (`DEB, RPM, WIX, DragNDrop, ...`) </br>
и разместить их на сервисе **GitHub**. (см. пример для [Travi CI](https://docs.travis-ci.com/user/deployment/releases))</br>

## Links

- [DMG](https://cmake.org/cmake/help/latest/module/CPackDMG.html)
- [DEB](https://cmake.org/cmake/help/latest/module/CPackDeb.html)
- [RPM](https://cmake.org/cmake/help/latest/module/CPackRPM.html)
- [NSIS](https://cmake.org/cmake/help/latest/module/CPackNSIS.html)

```
Copyright (c) 2015-2019 The ISC Authors
```
