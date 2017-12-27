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

Установка значений для сервиса **GitHub**

```ShellSession
$ export GITHUB_USERNAME=GolubDobra  # Устанавливаем значение переменной окружения GITHUB_USERNAME
```

Инициализация директории **lab04**

```ShellSession
$ git clone https://github.com/GolubDobra/lab03 lab04 # Клонируем репозиторий lab03 в директорию lab04
$ cd lab04 # Переходим в директорию lab04
$ git remote remove origin # Удалить старый репозиторий
$ git remote add origin https://github.com/GolubDobra/lab04 # Добавляем новый удаленный репозиторий
```

```ShellSession
$ g++ -I./include -std=c++11 -c sources/print.cpp # Подключаем библиотеки 11-го стандарта
$ ls print.o 
print.o
$ ar rvs print.a print.o # Создание библиотеки, используя утилиту Archiver
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

Удаление файлов

```ShellSession
$ rm -rf example1.o example2.o print.o # Объектные файлы
$ rm -rf print.a # Архивные файлы
$ rm -rf example1 example2 # .exe файлы
$ rm -rf log.txt # .txt файл
```
Работа с файлом *CMakeLists.txt*

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
$ cmake --build _build #Сборка и линковка проекта
```
Создаем исполняемые файл с именем example1 из исходника example1.cpp и example2 из исходника example2.cpp

```ShellSession
$ cat >> CMakeLists.txt <<EOF 

add_executable(example1 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example1.cpp) 
add_executable(example2 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example2.cpp) 
EOF
```
Линковка программ example1 и example2 с библиотекой print

```ShellSession
$ cat >> CMakeLists.txt <<EOF

target_link_libraries(example1 print) 
target_link_libraries(example2 print)
EOF
```
Собираем проект 

```ShellSession
$ cmake --build _build  #Запускаем сборку в каталоге _build
$ cmake --build _build --target print #Сборка цели print
$ cmake --build _build --target example1 #Сборка example1
$ cmake --build _build --target example2 #Сборка example2
```

```ShellSession
$ ls -la _build/libprint.a
$ _build/example1 && echo # Сборка и вывод на экран содержимое файла
hello
$ _build/example2 # Сборка example2
$ cat log.txt && echo
hello
```

```ShellSession
$ git clone https://github.com/tp-labs/lab04 tmp # Копирование 
$ mv -f tmp/CMakeLists.txt . # Перемещение в tmp/CMakeLists.txt .
$ rm -rf tmp # Удаление директории tmp
```
Настройки **CMake**

```ShellSession
$ cat CMakeLists.txt # Вывод содержимое файла
$ cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install # Сборка проекта с флагом -DCMAKE_INSTALL_PREFIX
$ cmake --build _build --target install # Полная сборка проекта print 
$ tree _install # Просмотр дерева вложенности файлов(расширенная версия ls *), аналогичная команда ls *

Отправляем последние изменения на **GitHub** сервер

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
