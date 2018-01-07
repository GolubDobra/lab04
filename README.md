Данная лабораторная работа посвещена изучению систем автоматизации сборки проекта на примере **CMake**

```ShellSession
$ open https://cmake.org/
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab04** на сервисе **GitHub**
- [x] 2. Ознакомиться со ссылками учебного материала
- [x] 3. Выполнить инструкцию учебного материала
- [x] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

#### Установка значений для сервиса **GitHub**

```ShellSession
$ export GITHUB_USERNAME=GolubDobra  # Устанавливаем значение переменной окружения GITHUB_USERNAME
```

#### Инициализация директории **lab04**

```ShellSession
$ git clone https://github.com/GolubDobra/lab03 lab04 # Клонируем репозиторий lab03 в директорию lab04
Клонирование в «lab04»…
remote: Counting objects: 20, done.
remote: Compressing objects: 100% (15/15), done.
remote: Total 20 (delta 2), reused 11 (delta 0), pack-reused 0
Распаковка объектов: 100% (20/20), готово.
Проверка соединения… готово.
$ cd lab04 # Переходим в директорию lab04
$ git remote remove origin # Удалить старый репозиторий
$ git remote add origin https://github.com/GolubDobra/lab04 # Добавляем новый удаленный репозиторий
```

```ShellSession
$ g++ -I./include -std=c++11 -c sources/print.cpp # Подключаем библиотеки 11-го стандарта
$ ls print.o 
print.o
$ ar rvs print.a print.o # Создание библиотеки, используя утилиту Archiver
ar: creating archive print.a
a - print.o
$ file print.a # Показать тип файла 
$ g++ -I./include -std=c++11 -c examples/example1.cpp # Сборка проекта
$ ls example1.o
example1.o
$ g++ example1.o print.a -o example1 # Сборка проекта
$ ./example1 && echo
hello
```

```ShellSession
$ g++ -I./include -std=c++11 -c examples/example2.cpp # Флаг I./ метка для заголовочных файлов
$ ls example2.o
example2.o
$ g++ example2.o print.a -o example2 # Сборка проекта
$ ./example2
$ cat log.txt && echo # Вывод на экран содержимое файла 
hello
```

#### Удаление файлов

```ShellSession
$ rm -rf example1.o example2.o print.o # Объектные файлы
$ rm -rf print.a # Архивные файлы
$ rm -rf example1 example2 # .exe файлы
$ rm -rf log.txt # .txt файл
```
#### Работа с файлом *CMakeLists.txt*

```ShellSession
$ cat > CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.0) # Проверка версии CMake
project(print) #название проекта
EOF
```
```ShellSession
$ cat >> CMakeLists.txt <<EOF
set(CMAKE_CXX_STANDARD 11) # Подключение 11-го стандарта
set(CMAKE_CXX_STANDARD_REQUIRED ON) # Активация 11-го стандарта
EOF
```

```ShellSession
$ cat >> CMakeLists.txt <<EOF
add_library(print STATIC ${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp) # Добавление статической библиотеки,используя указанные исходные файлы
EOF
```

```ShellSession
$ cat >> CMakeLists.txt <<EOF
include_directories(\${CMAKE_CURRENT_SOURCE_DIR}/include) # Список директорий,относительно которых следует искать заголовочные файлы
EOF
```

```ShellSession
$ cmake -H. -B_build # Сборка проекта в каталог _build где генерируются файлы проекта
-- The C compiler identification is GNU 5.4.0
-- The CXX compiler identification is GNU 5.4.0
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
-- Build files have been written to: /home/GolubDobra/lab04/_build
$ cmake --build _build #Сборка и линковка проекта
Scanning dependencies of target print
[ 50%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[100%] Linking CXX static library libprint.a
[100%] Built target print
```
#### Создаем исполняемые файл с именем example1 из исходника example1.cpp и example2 из исходника example2.cpp

```ShellSession
$ cat >> CMakeLists.txt <<EOF 

add_executable(example1 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example1.cpp) 
add_executable(example2 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example2.cpp) 
EOF
```
#### Линковка программ example1 и example2 с библиотекой print

```ShellSession
$ cat >> CMakeLists.txt <<EOF

target_link_libraries(example1 print) 
target_link_libraries(example2 print)
EOF
```
#### Собираем проект 

```ShellSession
$ cmake --build _build  #Запускаем сборку в каталоге _build
-- Configuring done
-- Generating done
-- Build files have been written to: /home/GolubDobra/lab04/_build
[ 33%] Built target print
Scanning dependencies of target example1
[ 50%] Building CXX object CMakeFiles/example1.dir/examples/example1.cpp.o
[ 66%] Linking CXX executable example1
[ 66%] Built target example1
Scanning dependencies of target example2
[ 83%] Building CXX object CMakeFiles/example2.dir/examples/example2.cpp.o
[100%] Linking CXX executable example2
[100%] Built target example2
$ cmake --build _build --target print #Сборка цели print
[100%] Built target print
$ cmake --build _build --target example1 #Сборка example1
[ 50%] Built target print
[100%] Built target example1
$ cmake --build _build --target example2 #Сборка example2
[ 50%] Built target print
[100%] Built target example2
```

```ShellSession
$ ls -la _build/libprint.a
-rw-rw-r-- 1 lightman1998 lightman1998 2302 сен 24 23:06 _build/libprint.a
$ _build/example1 && echo # Сборка и вывод на экран содержимое файла
hello
$ _build/example2 # Сборка example2
$ cat log.txt && echo
hello
```

```ShellSession
$ git clone https://github.com/tp-labs/lab04 tmp # Копирование 
Клонирование в «tmp»…
remote: Counting objects: 31, done.
remote: Total 31 (delta 0), reused 0 (delta 0), pack-reused 31
Распаковка объектов: 100% (31/31), готово.
Проверка соединения… готово.
$ mv -f tmp/CMakeLists.txt . # Перемещение в tmp/CMakeLists.txt .
$ rm -rf tmp # Удаление директории tmp
```
#### Настройки **CMake**

```ShellSession
$ cat CMakeLists.txt # Вывод содержимое файла
cmake_minimum_required(VERSION 3.0)
project(print)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
add_library(print STATIC /sources/print.cpp)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include)

add_executable(example1 ${CMAKE_CURRENT_SOURCE_DIR}/examples/example1.cpp) 
add_executable(example2 ${CMAKE_CURRENT_SOURCE_DIR}/examples/example2.cpp) 

target_link_libraries(example1 print) 
target_link_libraries(example2 print)
$ cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install # Сборка проекта с флагом -DCMAKE_INSTALL_PREFIX
DCMAKE_INSTALL_PREFIX=_install
-- Configuring done
-- Generating done
-- Build files have been written to: /home/GolubDobra/lab04/_build
$ cmake --build _build --target install # Полная сборка проекта print 
[100%] Built target print
Install the project...
-- Install configuration: ""
-- Installing: /home/GolubDobra/lab04/_install/lib/libprint.a
-- Installing: /home/GolubDobra/lab04/_install/include
-- Installing: /home/GolubDobra/lab04/_install/include/print.hpp
-- Installing: /home/GolubDobra/lab04/_install/cmake/print-config.cmake
-- Installing: /home/GolubDobra/lab04/_install/cmake/print-config-noconfig.cmake
$ tree _install # Просмотр дерева вложенности файлов(расширенная версия ls *), аналогичная команда ls *
_install
├── cmake
│ ├── print-config.cmake
│ └── print-config-noconfig.cmake
├── include
│ └── print.hpp
└── lib
  └── libprint.a
  3 directories, 4 files
```

#### Отправляем последние изменения на **GitHub** сервер

```ShellSession
$ git add CMakeLists.txt # Отследить изменения CMakeLists.txt 
$ git commit -m"added CMakeLists.txt" # Сохранить изменения CMakeLists.txt 
$ git push origin master # Выгрузка файлов на сервер
```

## Report

```ShellSession
$ cd ~/workspace/labs/
$ export LAB_NUMBER=04
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gistup -m "lab${LAB_NUMBER}"
```

## Links

- [Autotools](http://www.gnu.org/software/automake/manual/html_node/Autotools-Introduction.html)
- [CMake](https://cgold.readthedocs.io/en/latest/index.html)

```
Copyright (c) 2017 Братья Вершинины
```
