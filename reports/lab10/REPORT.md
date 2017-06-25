## Laboratory work X

Данная лабораторная работа посвещена изучению систем управления пакетами на примере **Hunter**

```ShellSession
$ open https://github.com/ruslo/hunter
```

## Tasks

- [V] 1. Создать публичный репозиторий с названием **lab10** на сервисе **GitHub**
- [V] 2. Выполнить инструкцию учебного материала
- [V] 3. Ознакомиться со ссылками учебного материала
- [V] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```ShellSession
$ export GITHUB_USERNAME=a346560
```

```ShellSession
$ wget https://github.com/${GITHUB_USERNAME}/lab09/archive/v0.1.0.0.tar.gz # Забрали пакет из ЛР9
$ export PRINT_SHA1=`openssl sha1 v0.1.0.0.tar.gz | cut -d'=' -f2 | cut -c2-41` # посчитали хеш
$ echo $PRINT_SHA1
43f2c155c9eef334815ad7c39686be57d5423c06

```

```ShellSession
$ git clone https://github.com/ruslo/hunter hunter #взяли hunter
$ cd hunter && git checkout v0.18.57 # проверили патчи
$ cd cmake
$ mkdir projects/print
$ cat > projects/print/hunter.cmake <<EOF # настройки
include(hunter_add_version)
include(hunter_cacheable)
include(hunter_cmake_args)
include(hunter_download)
include(hunter_pick_scheme)

hunter_add_version(
    PACKAGE_NAME
    print
    VERSION
    "0.1.0.0"
    URL
    "https://github.com/${GITHUB_USERNAME}/lab09/archive/v0.1.0.0.tar.gz"
    SHA1
    ${PRINT_SHA1}
)

hunter_pick_scheme(DEFAULT url_sha1_cmake)

hunter_cmake_args(
    print
    CMAKE_ARGS
    BUILD_EXAMPLES=NO
    BUILD_TESTS=NO
)
hunter_cacheable(print)
hunter_download(PACKAGE_NAME print)
EOF
$ cat >> configs/default.cmake <<EOF
hunter_config(print VERSION 0.1.0.0)
EOF
$ cd ../..
```

```ShellSession
$ export HUNTER_ROOT=`pwd`/hunter # задание рабочей директории
$ mkdir lab10 && cd lab10
$ git init
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab10
```

```ShellSession
$ mkdir sources
$ cat > sources/demo.cpp <<EOF
#include <print.hpp>

int main(int argc, char** argv) {
    std::string text;
    while(std::cin >> text) {
	std::ofstream out("log.txt", std::ios_base::app);
	print(text, out);
	out << std::endl;
    }
}
EOF
```

```ShellSession
$ wget https://github.com/hunter-packages/gate/archive/v0.8.1.tar.gz 
$ tar -xzvf v0.8.1.tar.gz gate-0.8.1/cmake/HunterGate.cmake
$ mkdir cmake
$ mv gate-0.8.1/cmake/HunterGate.cmake cmake
$ rm -rf gate*/
$ rm *.tar.gz
```

```ShellSession
$ cat > CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.0)

set(CMAKE_CXX_STANDARD 11)
EOF
```

```ShellSession
$ cat >> CMakeLists.txt <<EOF

include(cmake/HunterGate.cmake)

HunterGate(
    URL "https://github.com/ruslo/hunter/archive/v0.7.0.tar.gz"
    SHA1 "e730118c7ec65126398f8d4f09daf9366791ede0"
)
EOF
```

```ShellSession
$ cat >> CMakeLists.txt <<EOF

project(demo)

hunter_add_package(print)
find_package(print)

add_executable(demo \${CMAKE_CURRENT_SOURCE_DIR}/sources/demo.cpp)
target_link_libraries(demo print)

install(TARGETS demo RUNTIME DESTINATION bin)
EOF
```

```ShellSession
$ cat > .gitignore <<EOF
*build*/
*install*/
*.swp
EOF
```

```ShellSession
$ cat > README.md <<EOF
[![Build Status](https://travis-ci.org/${GITHUB_USERNAME}/lab10.svg?branch=master)](https://travis-ci.org/${GITHUB_USERNAME}/lab10)
the demo application redirects data from stdin to a file **log.txt** using a package **print**.
EOF
```

```ShellSession
$ cat > .travis.yml <<EOF
language: cpp

script:   
- cmake -H. -B_build
- cmake --build _build
EOF
```

```ShellSession
$ travis lint
Hooray, .travis.yml looks valid :)

```

```ShellSession
$ git add .
$ git commit -m"first commit"
$ git push origin master
```

```ShellSession
$ travis login --auto
We need your GitHub login to identify you.
This information will not be sent to Travis CI, only to api.github.com.
The password will not be displayed.

Try running with --github-token or --auto if you don't want to enter your password anyway.

Username: a346560
Password for a346560: ********
Successfully logged in as a346560!

$ travis enable
Detected repository as a346560/lab10, is this correct? |yes| 
a346560/lab10: enabled :)

```

```ShellSession
$ cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
-- [hunter] Calculating Config-SHA1
-- [hunter] Calculating Toolchain-SHA1
-- [hunter] HUNTER_ROOT: /home/user/vershinin/lab10/hunter
-- [hunter] [ Hunter-ID: xxxxxxx | Config-ID: 75ced31 | Toolchain-ID: b8de188 ]
-- [hunter] PRINT_ROOT: /home/user/vershinin/lab10/hunter/_Base/xxxxxxx/75ced31/b8de188/Install (ver.: 0.1.0.0)
-- Configuring done
-- Generating done
-- Build files have been written to: /home/user/vershinin/lab10/lab10/_build
$ cmake --build _build --target install
Scanning dependencies of target DEMO
[ 50%] Building CXX object CMakeFiles/DEMO.dir/sources/demo.cpp.o
[100%] Linking CXX executable DEMO
[100%] Built target DEMO
Install the project...
-- Install configuration: ""
-- Installing: /home/user/vershinin/lab10/lab10/_install/bin/DEMO
$ mkdir artifacts && cd artifacts
$ echo "text1 text2 text3" | ../_install/bin/demo
$ cat log.txt
text1
text2
text3
```

## Report

```ShellSession
$ cd ~/workspace/labs/
$ export LAB_NUMBER=10
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gistup -m"lab${LAB_NUMBER}"
```

## Links

- [polly](https://github.com/ruslo/polly)
- [conan](https://conan.io)

```
Copyright (c) 2017 Братья Вершинины
```
