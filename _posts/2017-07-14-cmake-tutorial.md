---
layout: post
title: CMake 简单用法
date: 2017-07-14 09:03:00 +08:00
tags: 初学者
---


***

### 一.概述 CMake

* Make 工具有很多,例如 `GNU Make`, QT 的 `QMake`, 微软的 `MS nmake`, `BSD Make(pmake)`, `Makepp` 等等. 这些工具遵循着不同的规范和标准,所执行的 Makefile 格式也千差万别, 如果要跨平台,就要为每一种标准写一次 Makefile.
* `CMake` 就是解决上述问题设计的工具. 首先, 允许开发者编写一种平台无关的 `CMakeLists.txt` 文件来定制真个编译流程,然后根据目标平台进一步生成所需的本地化 Makefile 和工程文件.
* `CMake` 作为项目架构系统的知名开源项目有 VTK, ITK, KDE, OpenCVC OSG 等.
* `CMake` 拥有比直接编写 `Makefile` 更直观的语法, 更易于编写和维护.

### 二.流程 CMake

* Linux 平台下使用 CMake 生成 Makefile 并编译的流程:
1. 编写 CMake 配置文件 CMakeLists.txt.
2. 执行命令 `cmake PATH` 生成 Makefile. PATH 是 CMakeLists.txt 所在的目录.
3. 使用 make 命令编译.

### 三.学习 CMake

#### 单个源文件

* 项目只有一个源文件 `main.c`, 该程序的用途是计算一个数的指数幂.
* 编写 `CMakeLists.txt`, 保存在与 `main.c` 源文件同个目录下:

```CMake
# CMake 最低版本号要求
cmake_minimum_required(VERSION 2.8)

# 项目信息
PROJECT(Demo1)

# 指定生成目标
add_executable(Demo1 main.c)
```

* CMakeLists.txt 语法比较简单, 有命令, 注释, 空格组成, **命令不区分大小写**. `#` 后面内容认为是注释. 命令由 **命令名称**, **小括号**, **参数** 组成, 参数之间用 **空格** 进行间隔.

* 上面的 CMakeLists.txt 文件, 依次出现的几个命令:
1. `cmake_minimum_required` : 运行次配置文件的 CMake 最低版本.
2. `project` : 参数值是 `Demo1`, 表示该项目的名称是 `Demo1`.
3. `add_executable` : 将 `main.c` 的源文件编译成一个名称为 `Demo1` 的可执行文件.

* 编译项目, 在当前目录执行 `cmake .`, 得到 Makefile 后再使用 `make` 命令编译得到 `Demo1` 可执行文件.

```bash
MacBookPro:demo1 srefan$ cmake .
-- The C compiler identification is AppleClang 8.1.0.8020042
-- The CXX compiler identification is AppleClang 8.1.0.8020042
-- Check for working C compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/cc
-- Check for working C compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++
-- Check for working CXX compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /Users/srefan/gitlab/cmake_tutorial/demo1

MacBookPro:demo1 srefan$ make
Scanning dependencies of target Demo1
[ 50%] Building C object CMakeFiles/Demo1.dir/main.c.o
[100%] Linking C executable Demo1
[100%] Built target Demo1

MacBookPro:demo1 srefan$ ./Demo1  5 3
5 ^ 3 is 125
MacBookPro:demo1 srefan$ ./Demo1  2 4
2 ^ 4 is 16
```

#### 同一目录,多个源文件

* 把 `power` 函数单独写进一个名为 `MathFunctions.c` 的源文件里, 工程变成如下形式:

```bash
./Demo2
    +--- main.c
    +--- MathFunctions.c
    +--- MathFunctions.h
```

* 编写 `CMakeLists.txt`, 保存在与 `main.c` 源文件同个目录下:

```CMake
# CMake 最低版本号要求
cmake_minimum_required(VERSION 2.8)

# 项目信息
PROJECT(Demo2)

# 指定生成目标
add_executable(Demo2 main.c MathFunctions.c)

```

* 唯一的改动只是在 `add_executable` 命令中增加了一个 `MathFunctions.c` 源文件.
* 但是如果源文件很多,更省事的方法是使用 `aux_source_directory` 命令, 该命令会查找指定目录下的所有源文件, 然后将结果存进指定变量名.

```CMake
aux_source_directory(<dir> <variable>)
```
* 修改 CMakeLists.txt 如下:

```CMake
# CMake 最低版本号要求
cmake_minimum_required(VERSION 2.8)

# 项目信息
PROJECT(Demo2)

# 将当前目录所有文件添加到变量 PROGRAM_SOURCE 中
aux_source_directory(. PROGRAM_SOURCE)

# 指定目标可执行文件 Demo2 的源代码文件为 PROGRAM_SOURCE
add_executable(Demo2 ${PROGRAM_SOURCE})
```

* `CMake` 会将当前目录所有源文件的文件名赋值给变量 `PROGRAM_SOURCE`, 再将变量 `PROGRAM_SOURCE` 中的源文件需要编译成一个名称为 `Demo2` 的可执行文件.

<!--```bash
MacBookPro:demo1 srefan$ cmake .
-- The C compiler identification is AppleClang 8.1.0.8020042
-- The CXX compiler identification is AppleClang 8.1.0.8020042
-- Check for working C compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/cc
-- Check for working C compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++
-- Check for working CXX compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /Users/srefan/gitlab/cmake_tutorial/demo2

MacBookPro:demo1 srefan$ make
Scanning dependencies of target Demo2
[ 33%] Building C object CMakeFiles/Demo2.dir/MathFunctions.c.o
[ 66%] Building C object CMakeFiles/Demo2.dir/main.c.o
[100%] Linking C executable Demo2
[100%] Built target Demo2

MacBookPro:demo1 srefan$ ./Demo2  5 3
5 ^ 3 is 125
MacBookPro:demo1 srefan$ ./Demo2  2 4
2 ^ 4 is 16
```-->

#### 多个目录,多个源文件

* 进一步将 `MathFunctions.h` 和 `MathFunctions.c` 文件移动到 `math` 目录下:

```bash
./Demo3
    +--- main.c
    +--- math/
          +--- MathFunctions.c
          +--- MathFunctions.h
```

* 这种情况, 需要在根目录 `Demo3` 和 `math` 目录里分别编写一个 `CMakeLists.txt` 文件, 为了方便, 先将 `math` 目录文件编译成静态库, 再由 `main` 函数调用.

* 根目录的 `CMakeLists.txt`:

```CMake
# CMake 最低版本号要求
cmake_minimum_required(VERSION 2.8)

# 项目信息
PROJECT(Demo3)

# 将当前目录所有文件添加到变量 PROGRAM_SOURCE 中
aux_source_directory(. PROGRAM_SOURCE)

# 添加 math 子目录
add_subdirectory(math)

# 指定生成目标
add_executable(Demo3 ${PROGRAM_SOURCE})

# 添加链接库
target_link_libraries(Demo3 MathFunctions)

# 添加外部链接库查找目录, CMAKE_CURRENT_SOURCE_DIR 是内置宏, 表示当前 CMakeLists.txt 所在目录
# link_directories(${CMAKE_CURRENT_SOURCE_DIR}/math)

# 链接指定目录指定某个库
# target_link_libraries(Demo3 ${CMAKE_CURRENT_SOURCE_DIR}/math/libMathFunctions.a)
```

* 使用命令 `add_subdirectory` 添加一个子目录 `math`, 这样 `math` 目录下的 `CMakeLists.txt` 文件和源代码也会被处理.
* 使用命令 `target_link_libraries` 指明可执行文件 `Demo3` 连接一个名为 `MathFunctions` 的链接库.
* 使用命令 `target_link_libraries` 可以链接指定目录指定某个库, 带库具体的路径即可.
* 使用命令 `link_directories` 添加外部链接库查找目录.

* 子目录的 `CMakeLists.txt`:

```CMake
aux_source_directory(<dir> <variable>)
```
* 修改 CMakeLists.txt 如下:

```CMake
# 查找当前目录下的所有源文件, 并将名称保存到 DIR_LIB_SRCS 变量
aux_source_directory(. DIR_LIB_SRCS)

# 生成链接库
add_library (MathFunctions STATIC ${DIR_LIB_SRCS})
```

* 命令 `add_library` 将当前子目录中的源文件编译为静态链接库, STATIC 表示创建静态库, 如果是 SHARED, 则为创建动态库.

#### 自定义编译选项

* `CMake` 允许为项目增加编译选项, 根据用户的环境和需求选择最合适的编译方案.
* 将 `MathFunctions` 库设为一个可选的库,如果选项为 `ON`, 使用库定义的数学函数进行运算, 否则用标准库中的数学函数库.

* 修改 CMakeLists.txt 如下:
 
```CMake
# CMake 最低版本号要求
cmake_minimum_required(VERSION 2.8)

# 项目信息
PROJECT(Demo4)

# 加入一个配置头文件，用于处理 CMake 对源码的设置
configure_file (
    "${PROJECT_SOURCE_DIR}/config.h.in"
    "${PROJECT_BINARY_DIR}/config.h"
)

# 是否使用自己的 MathFunctions 库
option (USE_MYMATH
    "Use provided math implementation" ON)

# 是否加入 MathFunctions 库
if (USE_MYMATH)
    include_directories ("${PROJECT_SOURCE_DIR}/math")
    add_subdirectory(math)
    set (EXTRA_LIBS ${EXTRA_LIBS} MathFunctions)
endif (USE_MYMATH)

# 编译选项的内置宏为 CMAKE_CXX_FLAGS, 只要将此宏设置为自定义的编译选项即可
# set(CMAKE_CXX_FLAGS “-std=c++11 -O2 -g”)

# 将当前目录所有文件添加到变量 PROGRAM_SOURCE 中
aux_source_directory(. PROGRAM_SOURCE)

# 指定生成目标
add_executable(Demo4 ${PROGRAM_SOURCE})

# 添加链接库
target_link_libraries(Demo4 ${EXTRA_LIBS})
```

* 命令 `configure_file` 用于加入一个配置头文件 `config.h`, 这个文件是从 `config.h.in` 生成, 通过这样的机制, 预定义一些参数和变量来控制代码配置.
* 命令 `option` 添加了一个 `USE_MYMATH` 选项, 默认值为 `ON`.
* 根据 `USE_MYMATH` 变量的值来决定是否使用自己编写的 `MathFunctions` 库.

* `CMakeLists.txt` 中引用了 `config.h` 文件, 这个文件预定义了 `USE_MYMATH` 的值.
* 我们不直接编写这个文件, 方便从 `CMakeLists.txt ` 导入配置, 编写一个 `config.h.in` 文件.

```config
#cmakedefine USE_MYMATH
```

* 修改 `main.c` 文件, 根据 `USE_MYMATH` 预定义的值来决定是否调用标准库还是 `MathFunctions` 库.

```c
#include "config.h"

#ifdef USE_MYMATH
  #include "math/MathFunctions.h"
#else
  #include 
#endif
```

* 这样, `CMake` 自动根据 `CMakeLists.txt` 配置文件中的设置自动生成 `config.h` 文件.

##### 编译项目

* 用交互式选择变量的值, 用 `ccmake PATH` 命令.
* 刚刚定义的 `USE_MYMATH` 选项, 设置后, 按 `c` 选项完成配置, 按 `g` 生成 `Makefile`.

* 试试设置 `USE_MYMATH` 为 `ON` 和 `OFF`:
* a. `USE_MYMATH` 为 `ON`:

```bash
MacBookPro:demo4 srefan$ ./Demo4 5 2
Now we use our own Math library.
5 ^ 2 is 25
```

* 此时 `config.h` 内容:

```c
#define USE_MYMATH
```
* b. `USE_MYMATH` 为 `OFF`:

```bash
MacBookPro:demo4 srefan$ ./Demo4 5 2
Now we use the standard library.
5 ^ 2 is 25
```

* 此时 `config.h` 内容:

```c
/* #undef USE_MYMATH */
```

![cmake_demo4][img_4]

#### 执行外部命令

* 当我们的代码在编译之前需要先执行一些外部命令, 比如说使用 `thrift` 接口需要先执行 `thrift` 的代码生成器生成代码时, 我们可以在 `CMakeLists.txt` 中添加以下代码, 当执行 cmake 命令 生成 Makefile 时, 该命令就会被执行:

```CMake
set(THRIFT_FILE ${CMAKE_CURRENT_SOURCE_DIR}/mythrift.thrift)
exec_program("thrift --gen cpp -o ${CMAKE_CURRENT_SOURCE_DIR} ${THRIFT_FILE}")
```

#### 安装和测试

* `CMake` 可以指定安装规则, 添加测试. 通过产生 `Makefile` 后使用 `make install` 和 `make test` 来执行. 不需要像 `GNU Makefile` 编写 `install` 和 `test` 两个伪目标和规则.

##### 定制安装规则

* 首先, 在 `math/CMakeLists.txt` 文件里添加: 指明 `MathFunctions` 库安装路径, `libMathFunctions.a` 文件被复制到 `/usr/local/bin` 中.  `MathFunctions.h` 文件被复制到 `/usr/local/include` 中.

```CMake
# 指定 MathFunctions 库的安装路径
install (TARGETS MathFunctions DESTINATION bin)
install (FILES MathFunctions.h DESTINATION include)
```

* 之后, 在 `CMakeLists.txt` 文件里添加: 指明 `Demo` 的安装路径, `Demo` 文件被复制到 `/usr/local/bin` 中, `config.h` 文件被复制到 `/usr/local/include` 中.

```CMake
# 指定安装路径
install (TARGETS Demo5 DESTINATION bin)
install (FILES "${PROJECT_BINARY_DIR}/config.h"
         DESTINATION include)
```

> `/usr/local/` 是默认安装到的根目录, 可以通过修改 `CMAKE_INSTALL_PREFIX` 变量的值来指定这些文件应该拷贝到哪个根目录.

```bash
MacBookPro:demo5 srefan$ make install
[ 50%] Built target MathFunctions
[100%] Built target Demo5
Install the project...
-- Install configuration: ""
-- Installing: /usr/local/bin/Demo5
-- Installing: /usr/local/include/config.h
-- Installing: /usr/local/bin/libMathFunctions.a
-- Installing: /usr/local/include/MathFunctions.h

MacBookPro:demo5 srefan$ ls /usr/local/bin
Demo  libMathFunctions.a

MacBookPro:demo5 srefan$ ls /usr/local/include
config.h  MathFunctions.h
```

##### 添加测试

* `CMake` 提供了 `CTest` 测试工具, 我们要在项目根目录的 `CMakeLists.txt` 文件里调用 `add_test` 命令.

```bash
# 启用测试
enable_testing()

# 测试程序是否成功运行
add_test (test_run Demo5 5 2)

# 测试帮助信息是否可以正常提示
add_test (test_usage Demo5)
set_tests_properties (test_usage
  PROPERTIES PASS_REGULAR_EXPRESSION "Usage: .* base exponent")
  
# 测试 5 的平方
add_test (test_5_2 Demo5 5 2)
set_tests_properties (test_5_2
 PROPERTIES PASS_REGULAR_EXPRESSION "is 25")
 
# 测试 10 的 5 次方
add_test (test_10_5 Demo5 10 5)
set_tests_properties (test_10_5
 PROPERTIES PASS_REGULAR_EXPRESSION "is 100000")
 
# 测试 2 的 10 次方
add_test (test_2_10 Demo5 2 10)
set_tests_properties (test_2_10
 PROPERTIES PASS_REGULAR_EXPRESSION "is 1024")
```

* `test_run` 用来测试程序是否成功运行并返回 0 值.
* `test_usage` 用来测试帮助信息是否正常显示.
* 剩下3个测试用例 用来测试是否得到正确的结果.

> `PASS_REGULAR_EXPRESSION` 用来测试输出是否包含后面的字符串.

```bash
MacBookPro:demo5_2 srefan$ make test
Running tests...
Test project /Users/srefan/gitlab/cmake_tutorial/demo5_2
    Start 1: test_run
1/5 Test #1: test_run .........................   Passed    0.01 sec
    Start 2: test_usage
2/5 Test #2: test_usage .......................   Passed    0.00 sec
    Start 3: test_5_2
3/5 Test #3: test_5_2 .........................   Passed    0.00 sec
    Start 4: test_10_5
4/5 Test #4: test_10_5 ........................   Passed    0.00 sec
    Start 5: test_2_10
5/5 Test #5: test_2_10 ........................   Passed    0.01 sec

100% tests passed, 0 tests failed out of 5

Total Test time (real) =   0.04 sec
```

* 如果要测试更多的输入数据, 像上面那样一个个写测试用例未免太繁琐. 这时可以通过编写宏来实现.

```bash
# 定义一个宏，用来简化测试工作
macro (do_test arg1 arg2 result)
  add_test (test_${arg1}_${arg2} Demo5 ${arg1} ${arg2})
  set_tests_properties (test_${arg1}_${arg2}
    PROPERTIES PASS_REGULAR_EXPRESSION ${result})
endmacro (do_test)
 
# 使用该宏进行一系列的数据测试
do_test (5 2 "is 25")
do_test (10 5 "is 100000")
do_test (2 10 "is 1024")
```

> 关于 CTest 的更详细的用法可以通过 man 1 ctest 参考 CTest 的文档.

##### 支持gdb

* `CMake` 支持 gdb, 只需要指定 `Debug` 模式下开启 `-g` 选项, 之后可以直接对生成的程序使用 `gdb` 来调试.

```CMake
set(CMAKE_BUILD_TYPE "Debug")
set(CMAKE_CXX_FLAGS_DEBUG "$ENV{CXXFLAGS} -O0 -Wall -g -ggdb")
set(CMAKE_CXX_FLAGS_RELEASE "$ENV{CXXFLAGS} -O3 -Wall")
```

#### 添加环境检查

* 使用平台相关的特性时, 要对系统环境做检查. 例如: 检查系统是否自带 `pow` 函数, 带 `pow`, 就使用, 否则, 用自定义的 `power` 函数.

##### 添加 CheckFunctionExists 宏

* 首先, 在根目录 `CMakeLists.txt` 文件中添加 `CheckFunctionExists.cmake` 宏
* 然后, 调用 `check_function_exists` 命令测试 **链接器** 是否能够在 `链接阶段` 找到 `pow` 函数.

```CMake
# 检查系统是否支持 pow 函数
include (${CMAKE_ROOT}/Modules/CheckFunctionExists.cmake)
check_function_exists (pow HAVE_POW)
```
> 要将检查代码放在 `configure_file` 命令前.

##### 预定义相关宏变量

* 修改 `config.h.in` 文件, 预定义相关的宏变量.

```CMake
// does the platform provide pow function?
#cmakedefine HAVE_POW
```

##### 在代码中使用宏和函数

* 修改 `main.c`, 在代码中使用宏和函数.

```c
#ifdef HAVE_POW
    printf("Now we use the standard library. \n");
    double result = pow(base, exponent);
#else
    printf("Now we use our own Math library. \n");
    double result = power(base, exponent);
#endif
```

#### 添加版本号

* 指定项目的主版本号和副版本号, 在根目录 `CMakeLists.txt` 文件, 在 `project` 命令之后添加:

```CMake
set (Demo_VERSION_MAJOR 1)
set (Demo_VERSION_MINOR 0)
```

* 代码中获取版本, 修改 `config.h.in` 文件, 添加两个预定义变量:

```config
// the configured options and settings for Tutorial
#define Demo_VERSION_MAJOR @Demo_VERSION_MAJOR@
#define Demo_VERSION_MINOR @Demo_VERSION_MINOR@
```

#### 生成安装包

* 配置生成各种平台上的安装包, 包括二进制安装包和源码安装包. 用到 `CPack`, 它同样也是由 `CMake` 提供的一个工具, 专门用于打包.

* 在根目录的 `CMakeLists.txt` 文件尾部添加下面几行:

```CMake
# 构建一个 CPack 安装包
include (InstallRequiredSystemLibraries)
set (CPACK_RESOURCE_FILE_LICENSE
  "${CMAKE_CURRENT_SOURCE_DIR}/License.txt")
set (CPACK_PACKAGE_VERSION_MAJOR "${Demo_VERSION_MAJOR}")
set (CPACK_PACKAGE_VERSION_MINOR "${Demo_VERSION_MINOR}")
include (CPack)
```

* 导入 `InstallRequiredSystemLibraries` 模块, 以便之后导入 `CPack` 模块.
* 设置一些 `CPack` 相关变量, 包括版权信息和版本信息, 其中版本信息用了上一节定义的版本号.
* 导入 `CPack` 模块.

* 像往常一样构建工程, 并执行 `cpack` 命令.

##### 生成二进制安装包

```bash
cpack -C CPackConfig.cmake
```

##### 生成源码安装包

```bash
cpack -C CPackSourceConfig.cmake
```

```bash
MacBookPro:demo8 srefan$ cpack -C CPackConfig.cmake
CPack: Create package using STGZ
CPack: Install projects
CPack: - Run preinstall target for: Demo8
CPack: - Install project: Demo8
CPack: Create package
CPack: - package: /Users/srefan/gitlab/cmake_tutorial/demo8/Demo8-1.0.1-Darwin.sh generated.
CPack: Create package using TGZ
CPack: Install projects
CPack: - Run preinstall target for: Demo8
CPack: - Install project: Demo8
CPack: Create package
CPack: - package: /Users/srefan/gitlab/cmake_tutorial/demo8/Demo8-1.0.1-Darwin.tar.gz generated.

MacBookPro:demo8 srefan$ ls Demo8-*
Demo8-1.0.1-Darwin.sh  Demo8-1.0.1-Darwin.tar.gz
```

* 两个二进制包文件内容完全相同, 执行其中之一. 出现由 `CPack` 自动生成的交互式安装界面:

```bash
MacBookPro:demo8 srefan$ ./Demo8-1.0.1-Darwin.sh
Demo8 Installer Version: 1.0.1, Copyright (c) Humanity
This is a self-extracting archive.
The archive will be extracted to: /Users/everyoo/gitlab/cmake_tutorial/demo8

If you want to stop extracting, please press <ctrl-C>.
The MIT License (MIT)

Copyright (c) 2013 Joseph Pan(http://hahack.com)

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of
the Software, and to permit persons to whom the Software is furnished to do so,
subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS
FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR
COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER
IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.


Do you accept the license? [yN]:
y

By default the Demo8 will be installed in:
  "/Users/everyoo/gitlab/cmake_tutorial/demo8/Demo8-1.0.1-Darwin"
Do you want to include the subdirectory Demo8-1.0.1-Darwin?
Saying no will install in: "/Users/everyoo/gitlab/cmake_tutorial/demo8" [Yn]:
y

Using target directory: /Users/everyoo/gitlab/cmake_tutorial/demo8/Demo8-1.0.1-Darwin
Extracting, please wait...

Unpacking finished successfully
```

* 完成后提示安装到了 `Demo8-1.0.1-Darwin` 子目录中, 我们可以进去执行该程序:

```bash
MacBookPro:demo8 srefan$ ./Demo8-1.0.1-Darwin/bin/Demo8 5 2
Now we use the standard library.
5 ^ 2 is 25
```

> 关于 `CPack` 的更详细的用法可以通过 `man 1 cpack` 参考 CPack 的文档.

#### 将其他平台的项目迁移到 CMake

##### autotools

* `am2cmake` 将 autotools 系项目转换到 CMake, 成功案例: KDE.
* `Alternative Automake2CMake` 可以转换使用 automake 的 KDevelop 工程项目.
* `Converting autoconf tests`.

##### qmake

* `qmake converter` 可以转换使用 QT 的 qmake 的工程

##### Visual Studio

* `vcproj2cmake.rb` 可以根据 Visual Studio 的工程文件(后缀名是 `.vcproj` 或 `.vcxproj`)生成 CMakeLists.txt 文件.
* `vcproj2cmake.ps1` vcproj2cmake 的 PowerShell 版本.
* `folders4cmake` 根据 Visual Studio 项目文件生成相应的 `source_group` 信息, 这些信息可以很方便的在 CMake 脚本中使用. 支持 Visual Studio 9/10 工程文件.

##### CMakeLists.txt 自动推导

* `gencmake` 根据现有文件推导 CMakeLists.txt 文件.
* `CMakeListGenerator` 应用一套文件和目录分析创建出完整的 CMakeLists.txt 文件. 仅支持 Win32 平台.

### 五.参考链接

此文参考于 [hahack.com][Link_1],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

官方API文档 [在此处, 点击查看][Link_3].

本文的 `代码` 在此处, [点击下载][Link_2]: 
> [https://github.com/yangbryant/cmake_tutorial][Link_2]

***

[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[Link_1]: http://www.hahack.com/codes/cmake/
[Link_2]: https://github.com/yangbryant/cmake_tutorial

[img_4]: /assets/images/cmake/cmake_demo4.jpg 'demo4'

[Link_3]: https://cmake.org/cmake-tutorial/
