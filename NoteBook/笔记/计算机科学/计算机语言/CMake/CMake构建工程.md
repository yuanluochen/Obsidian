# CMake

```cmake
PROJECT (HELLO)

SET(SRC_LIST test.cpp)
MESSAGE(STATUS "This is Binary dir" ${HELLO_BINARY_DIR})
MESSAGE(STATUS "This is Source dir" ${HELLO_SOURCE_DIR})
ADD_EXECUTABLE(hello ${SRC_LIST})

```

## 语法

### PROJECT 关键字

可以用来制定工程的名字和支持的语言，默认支持所有语言

PROJECT （HELLO） 指定工程名字，并且支持所有语言  
PROJECT （HELLO CXX） 指定工程名字，并且支持语言是c++  
PROJECT  ( HELLO C CXX) 指定工程名字，并且支持语言是c和c++  

该指令隐式定义了两个CMAKE变量
```cmake
<projectname>_BINARY_DIR #本例中是 HELLO_BINARY_DIR
<projectname>_SOURCE_DIR #本例中是 HELLO_SOURCE_DIR
```

MESSAGE 关键字就可以直接使用这两个变量，当前都指向当前工作目录  
但是这两个变量有问题，如果工程名变了，以上两个隐式定义的变量名就变了，所以为防止以上问题，cmake又定义了两个预定义变量：PROJECT_BINARY_DIR 和 PROJECT_SOURCE_DIR, 其内容与HELLO_BINARY_DIR 和 HELLO_SOURCE_DIR 内容一致

### SET 关键字

显示指定变量

```CMake
SET(SRC_LIST test.cpp) #SRC_LIST 变量就包含了test.cpp
# 也可以
SET（SRC_LIST main.cpp test.cpp)
```

### MESSAGE 关键字

向终端输出用户自定义信息  

主要包含三种信息  
+ SEND_ERROR 产生错误，生成过程被跳过
+ SATUS 输出前缀为 - 的信息
+ FATAL_ERROR 立即终止所有cmake过程

### ADD_EXECUTABLE 关键字

生成可执行文件

```CMake
	ADD_EXECUTABLE(hello ${SRC_LIST}) # 生成可执行文件为hello, 源文件读取变量SRC_LIST的内容
	#也可以直接写为 ADD_EXECUTABLE(hello main.cpp)
```

上面的例子可以简化为
```CMake
PROJECT(HELLO)
ADD_EXECUTABLE(hello test.cpp)
```

> 注意：工程名的HELLO 与 生成的可执行文件hello 没有任何关系

## 语法基本原则

+ 变量使用${} 方式取值 但是在 IF 控制 语句中是直接使用变量名
+ 指令（参数1 参数2……） 参数使用弧括号括起，参数之间使用空格或分号分开
+ 指令是大小无关的，参数和变量是大小写相关的，

## 语法注意事项

+  SET(SRC_LIST main.cpp) 可以写成 SET（SRC_LIST "main.cpp")，如果源文件有空间，就必须加双引号
+ ADD_EXECUTABLE(hello main) 后缀可以没有，他会自动寻找


## 内部构建和外部构建

外部构建，把临时文件放在build目录下，不会对源文件有任何影响强类使用外部构造方式

### 外部构建

外部构建，创建一个build目录，在该目录内cmake 将cmake的临时文件放于该目录内

1. 建立一个build目录，可以放在任何地方，建议放在当前目录下
2. 进入build目录，运行cmake .. , 这样cmake产生的文件都在build目录下
3. build目录下，运行make来构建工程

## 创建一个工程型的Hello World

+ 为工程添加一个子目录 src，用来放置工程源代码
+ 添加一个子目录doc，用来放置这个工程的文档 hello.txt
+ 在工程目录添加markdowm 文件 readme.md
+ 在工程目录添加一个runhello.sh 脚本，用来调用hello 二进制
+ 将构建后的目标文件放入构建目录的bin子目录
+ 将doc目录的内容以及readme.md 安装到 /usr/share/doc/cmake

### 将目标文件放入构建目录的bin子文件

> 每一个目录都要有CMakeLists.txt 说明，且每一个目录下的CMakeList.txt 要有关联

![](../../../../resources/Pasted%20image%2020221222212302.png)

外层CMakeList.txt

```cmake
PROJECT(HELLO)
ADD_SUBDIRECTORY(src bin) #与src文件下的CMakeLists.txt产生关联，指定生存的二进制文件存放在bin目录下
```

src下的CMakeList.txt

```cmake
ADD_EXECUTABLE(hello main.cpp)
```

### ADD_SUBDIRECTORY 指令

> 在最外层的CMakeLists.txt 编写的

```cmake
ADD_SUBDIRECTORY(source_dir [binary_dir] [EXCLUDE_FROM_ALL])
```

- 这个指令用于向当前工程添加存放源文件的子目录，并可以指定中间二进制和目标二进制存放的位置
- EXCLUDE_FROM_ALL 函数是将写的目录从编译中排除
- ADD_SUBDIRECTORY(src bin)
将src子目录加入工程并指定编译输出的路径为bin目录，如果不进行bin目录指定，那么编译结果（包括中间结果）都存放在build/src目录

### 更改二进制保存路径

SET 指令重新定义 EXECUTABLE_OUTPUT_PATH 和 LIBRARY_OUTPUT_PATH 变量 来指定最终的目标二进制的位置

```CMake
SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin
SET(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib
```

## 安装 HelloWorld

使用CMAKE 的一个新的指令: INSTALL
INSTALL 的安装包括：二进制 动态库 静态库 以及文件 目录 脚本

### 安装文件COPYRIGHT 和 README

```CMake
INSTALL(FILES COPYRIGHT README DSETINATION share/doc/cmake/)
```

FILES: 文件  
DESTINATION：  
1. 写绝对路径
2. 可写相对路径， 相对路径实际路径是: ${CMAKE_INSTALLL_PREFIX}/DESTIANTION 定义的路径
CMAKE_INSTALL_PREFIX 默认在/usr/local/


## 静态库和动态库的构建

1. 静态库一般 .a .lib 动态库一般 .so .dll
2. 静态库在编译时会直接整合到目标程序中，编译成功的可执行文件可以独立运行
3. 动态库在编译时不会放到链接的目标程序中，即可执行文件无法单独执行

### ADD_LIBRARY

ADD_LIBRARY(hello SHARED ${LIBHELLO_SRC})  
hello 库名，在生成的名字前面加lib，最终产生的文件时libhello.so
SHARED 动态库 STATIC 静态库
${LIBHELLO_SRC} 源文件

## 同时构建静态库和动态库

```CMake
#如果两个库的名字是相同的，那么只会构建一个，只会构建前一个
ADD_LIBRARY(hello SHARED ${LIBHELLO_SRC})
ADD_LIBRARY(hello STATIC ${LIBHELLO_SRC})

#修改静态库的名称，使两者名称不同可以实现
ADD_LIBRARY(hello SHARED ${LIBHELLO_SRC})
ADD_LIBRARY(hello_static STATIC ${LIBHELLO_SRC})
```

如果想让两者名称相同，则要使用如下方式解决

### SET_TARGRT_PROPERTIES

这条指令可以用来设置输出名称，对用动态库，还可以用来指定动态库的版本和API版本

同时构建静态和动态库

![](../../../../resources/Pasted%20image%2020221222225231.png)

![](../../../../resources/Pasted%20image%2020221222225416.png)

库的版本号
## 使用外部共享库和头文件
在CMakeLists.txt 加入头文件搜索路径

```cmake
INCLUDE_DIRECTORIES(include_path)
```

