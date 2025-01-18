      CMake中的命令**cmake\_minimum\_required用于设定需要的最低版本的CMake**。其格式如下：

```bash
cmake_minimum_required(VERSION <min>[...<policy_max>] [FATAL_ERROR])
```

      可选的<policy\_max>项在3.12版本中引入。**设置project所需的最低cmake版本，还会更新policy设置**。<min>和可选的<policy\_max>都是major.minor\[.patch\[.tweak\]\]形式的CMake版本，而"..."是literal，低版本和高版本之间的连接符号。  
      **如果CMake运行的版本低于<min>要求的版本，它将停止处理project并报告错误**。如果指定可选的<policy\_max>版本，它必须至少是<min>版本并影响Policy Settings中所述的policy设置。如果CMake的运行版本低于3.12(older than 3.12)，则额外的"..."点将被视为版本组件分隔符(version component separator)，导致...<policy\_max>部分被忽略并保留基于<min>的policy的3.12之前的行为。

```bash
cmake_minimum_required(VERSION 3.12...3.24)
```

      此命令会将CMAKE\_MINIMUM\_REQUIRED\_VERSION变量的值设置为<min>

```bash
cmake_minimum_required(VERSION 3.13)

message("CMAKE_MINIMUM_REQUIRED_VERSION: ${CMAKE_MINIMUM_REQUIRED_VERSION}")
```

      FATAL\_ERROR选项被CMake 2.6及更高版本接受但被忽略(accepted but ignored)。当CMake版本为2.4或更低版本时应该指定它，此时会fail并触发error，而不仅仅是warning。

```bash
cmake_minimum_required(VERSION 3.14 FATAL_ERROR)

cmake_minimum_required(VERSION 2.4 FATAL_ERROR)
```

      注意：  
      (1).**在调用project命令之前，在顶层(top-level)CMakeLists.txt文件的开头调用cmake\_minimum\_required命令**。在调用可能影响其行为的其它命令之前，建立(establish)版本和policy设置是非常重要的。  
      (2).在function内调用cmake\_minimum\_required会限制调用此function时对function范围的一些影响。例如，CMAKE\_MINIMUM\_REQUIRED\_VERSION变量不会在调用范围内设置。function不会引入自己的policy范围，因此调用者的policy设置会受到影响。通常，**不鼓励在function调用cmake\_minimum\_required**。如果在function内调用cmake\_minimum\_required，那么此命令只在function内起作用。

```bash
function(foo)

cmake_minimum_required(VERSION 3.14)

message("CMAKE_MINIMUM_REQUIRED_VERSION: ${CMAKE_MINIMUM_REQUIRED_VERSION}")

endfunction()

message("CMAKE_MINIMUM_REQUIRED_VERSION: ${CMAKE_MINIMUM_REQUIRED_VERSION}")

foo()

message("CMAKE_MINIMUM_REQUIRED_VERSION: ${CMAKE_MINIMUM_REQUIRED_VERSION}")
```

      Policy Settings：  
      **cmake\_minimum\_required(VERSION)命令隐式调用cmake\_policy(VERSION)命令**来指定当前project code是为给定的CMake版本范围而写的(is written for the given range of CMake versions)。CMake的运行版本已知的所有policy，并在<min>(或<policy\_max>,如果指定)版本或更早版本中引入的所有policy都将设置为使用NEW行为。更高版本中引入的所有policy均未设置(unset)。这有效请求了给定CMake版本的首选行为(behavior preferred)，并告诉较新的CMake版本警告其新policy。  
      当指定的<min>版本高于2.4时，该命令会隐式调用：cmake\_policy(VERSION <min>\[...<max>\])，它根据指定的版本范围设置CMake policy。  
      当指定的<min>版本为2.4，或更低版本时，该命令会隐式调用：cmake\_policy(VERSION 2.4\[...<max>\])，这为CMake 2.4及更低版本启用兼容功能(compatibility features)。

```bash
cmake_minimum_required(VERSION 3.14...3.20)

message("CMAKE_MINIMUM_REQUIRED_VERSION: ${CMAKE_MINIMUM_REQUIRED_VERSION}")

cmake_policy(GET CMP0081 var)

message("var: ${var}")

cmake_policy(GET CMP0126 var)

message("var: ${var}")

cmake_policy(GET CMP0108 var)

message("var: ${var}")

cmake_minimum_required(VERSION 3.14)

message("CMAKE_MINIMUM_REQUIRED_VERSION: ${CMAKE_MINIMUM_REQUIRED_VERSION}")

cmake_policy(GET CMP0126 var)

message("var: ${var}")
```

      执行上述测试代码需要3个文件：build.sh, [CMakeLists](https://so.csdn.net/so/search?q=CMakeLists&spm=1001.2101.3001.7020).txt, test\_cmake\_minimum\_required.cmake

     build.sh内容如下：

```bash
#! /bin/bash

params=(function macro cmake_parse_arguments \

find_library find_path find_file find_program find_package \

cmake_policy cmake_minimum_required project include \

string)

usage()

{

echo "Error: $0 needs to have an input parameter"

echo "supported input parameters:"

for param in ${params[@]}; do

echo " $0 ${param}"

done

exit -1

}

if [ $# != 1 ]; then

usage

fi

flag=0

for param in ${params[@]}; do

if [ $1 == ${param} ]; then

flag=1

break

fi

done

if [ ${flag} == 0 ]; then

echo "Error: parameter \"$1\" is not supported"

usage

exit -1

fi

if [[ ! -d "build" ]]; then

mkdir build

cd build

else

cd build

fi

echo "==== test $1 ===="

cmake -DTEST_CMAKE_FEATURE=$1 ..
```

      CMakeLists.txt内容如下：

```bash
cmake_minimum_required(VERSION 3.22)

project(cmake_feature_usage)

message("#### current cmake version: ${CMAKE_MAJOR_VERSION}.${CMAKE_MINOR_VERSION}.${CMAKE_PATCH_VERSION}")

include(test_${TEST_CMAKE_FEATURE}.cmake)

message("==== test finish ====")
```

      test\_cmake\_minimum\_required.cmake：为上面的所有示例代码

      执行可能的结果如下图所示：

![](https://i-blog.csdnimg.cn/blog_migrate/e86763601ff75f88e32d8e54c6c3f9d6.png)

