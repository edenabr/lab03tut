# Lab03 Tutorial

## 1. Настройка Git

```bash
git config --global user.name "edenabr"
git config --global user.email "edenabramova@gmail.com"
```

Данными командами задаётся имя пользователя и электронная почта для Git.

---

## 2. Переход в директорию проекта

```bash
cd "/home/vboxuser/Рабочий стол/project/projects/lab03"
```
---
## 3. Исправление ошибки совместимости с компилятором

```bash
sed -i 's/std::sqrtf/std::sqrt/g' solver_lib/solver.cpp
```

В файле `solver_lib/solver.cpp` функция `std::sqrtf` была заменена на `std::sqrt`, так как используемый компилятор не находил `std::sqrtf`.

```bash
grep -q '#include <cmath>' solver_lib/solver.cpp || sed -i '1i #include <cmath>' solver_lib/solver.cpp
```

Данная команда проверяет, подключена ли библиотека `<cmath>`.
Если строки `#include <cmath>` нет, она добавляется в начало файла. Эта библиотека нужна для использования функции `std::sqrt`.

---

## 4. Создание файла CMakeLists.txt

```bash
cat > CMakeLists.txt <<'EOF'
cmake_minimum_required(VERSION 3.10)

project(lab03)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(formatter STATIC
    ${CMAKE_CURRENT_SOURCE_DIR}/formatter_lib/formatter.cpp
)

target_include_directories(formatter PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}/formatter_lib
)

add_library(formatter_ex STATIC
    ${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex_lib/formatter_ex.cpp
)

target_include_directories(formatter_ex PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex_lib
)

target_link_libraries(formatter_ex PUBLIC formatter)

add_library(solver STATIC
    ${CMAKE_CURRENT_SOURCE_DIR}/solver_lib/solver.cpp
)

target_include_directories(solver PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}/solver_lib
)

add_executable(hello_world
    ${CMAKE_CURRENT_SOURCE_DIR}/hello_world_application/hello_world.cpp
)

target_link_libraries(hello_world formatter_ex)

add_executable(equation
    ${CMAKE_CURRENT_SOURCE_DIR}/solver_application/equation.cpp
)

target_link_libraries(equation solver formatter_ex)

install(TARGETS formatter formatter_ex solver hello_world equation
    RUNTIME DESTINATION bin
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
)

install(DIRECTORY formatter_lib/
    DESTINATION include
    FILES_MATCHING PATTERN "*.h"
)

install(DIRECTORY formatter_ex_lib/
    DESTINATION include
    FILES_MATCHING PATTERN "*.h"
)

install(DIRECTORY solver_lib/
    DESTINATION include
    FILES_MATCHING PATTERN "*.h"
)
EOF
```


---

## 5. Очистка старых файлов сборки

```bash
rm -rf _build _install
```
---

## 6. Конфигурация проекта через CMake

```bash
cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
```
---

## 7. Сборка проекта

```bash
cmake --build _build
```
В результате были собраны:

```text
libformatter.a
libformatter_ex.a
libsolver.a
hello_world
equation
```

<details>
<summary>Сборка проекта</summary>
<pre>
-- The C compiler identification is GNU 15.2.0
-- The CXX compiler identification is GNU 15.2.0
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done (0.2s)
-- Generating done (0.0s)
-- Build files have been written to: /home/vboxuser/Рабочий стол/project/projects/lab03/_build
[ 10%] Building CXX object CMakeFiles/formatter.dir/formatter_lib/formatter.cpp.o
[ 20%] Linking CXX static library libformatter.a
[ 20%] Built target formatter
[ 30%] Building CXX object CMakeFiles/formatter_ex.dir/formatter_ex_lib/formatter_ex.cpp.o
[ 40%] Linking CXX static library libformatter_ex.a
[ 40%] Built target formatter_ex
[ 50%] Building CXX object CMakeFiles/solver.dir/solver_lib/solver.cpp.o
[ 60%] Linking CXX static library libsolver.a
[ 60%] Built target solver
[ 70%] Building CXX object CMakeFiles/hello_world.dir/hello_world_application/hello_world.cpp.o
[ 80%] Linking CXX executable hello_world
[ 80%] Built target hello_world
[ 90%] Building CXX object CMakeFiles/equation.dir/solver_application/equation.cpp.o
[100%] Linking CXX executable equation
[100%] Built target equation</pre>
</details>

---

## 8. Проверка работы программы hello_world

```bash
_build/hello_world
```

Команда запускает собранную программу `hello_world`.

---

## 9. Проверка работы программы equation

```bash
echo "1 2 1" | _build/equation
```
```sh
-------------------------
x1 = -1.000000
-------------------------
-------------------------
x2 = -1.000000
-------------------------
```
---

## 10. Установка проекта +
Просмотр структуры установленного проекта
```bash
cmake --build _build --target install
tree _install
```

<details>
<summary>Название команды</summary>
<pre>
[ 20%] Built target formatter
[ 40%] Built target formatter_ex
[ 60%] Built target solver
[ 80%] Built target hello_world
[100%] Built target equation
Install the project...
-- Install configuration: ""
-- Installing: /home/vboxuser/Рабочий стол/project/projects/lab03/_install/lib/libformatter.a
-- Installing: /home/vboxuser/Рабочий стол/project/projects/lab03/_install/lib/libformatter_ex.a
-- Installing: /home/vboxuser/Рабочий стол/project/projects/lab03/_install/lib/libsolver.a
-- Installing: /home/vboxuser/Рабочий стол/project/projects/lab03/_install/bin/hello_world
-- Installing: /home/vboxuser/Рабочий стол/project/projects/lab03/_install/bin/equation
-- Installing: /home/vboxuser/Рабочий стол/project/projects/lab03/_install/include
-- Installing: /home/vboxuser/Рабочий стол/project/projects/lab03/_install/include/formatter.h
-- Up-to-date: /home/vboxuser/Рабочий стол/project/projects/lab03/_install/include
-- Installing: /home/vboxuser/Рабочий стол/project/projects/lab03/_install/include/formatter_ex.h
-- Up-to-date: /home/vboxuser/Рабочий стол/project/projects/lab03/_install/include
-- Installing: /home/vboxuser/Рабочий стол/project/projects/lab03/_install/include/solver.h
_install
├── bin
│   ├── equation
│   └── hello_world
├── include
│   ├── formatter_ex.h
│   ├── formatter.h
│   └── solver.h
└── lib
    ├── libformatter.a
    ├── libformatter_ex.a
    └── libsolver.a

4 directories, 8 files</pre>
</details>

---

## 11. Проверка состояния репозитория

```bash
git status
```
---


## 12. Создание коммита

```bash
git commit -m "added CMakeLists.txt"
```

---

## 13. Переименование ветки в main

```bash
git branch -M main
```
---

## 14. Отправка изменений на GitHub

```bash
git push -u origin main
```

## Итог

В ходе выполнения tutorial был создан файл `CMakeLists.txt`, описывающий сборку проекта через CMake. Были добавлены статические библиотеки `formatter`, `formatter_ex`, `solver`, а также исполняемые файлы `hello_world` и `equation`.
