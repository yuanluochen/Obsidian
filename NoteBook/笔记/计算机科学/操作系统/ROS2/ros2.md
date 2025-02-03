# 节点

## 节点相关的CLIj

- 运行节点 ros2 run package_name executable_name
- 查看节点列表 ros2 node list
- 查看节点信息 ros2 node info node_name
- 映射节点名称 ros2 run turtlesim turtlesim_node --ros-args --remap __node:=my_turtle

## colcon

+ 只编译一个包 colcon test --packages-select YOUR_PKG_NAME
+ 不编译测试单元 colcon test --packages-select YOUR_PKG_NAME --cmake-args -DBULIB_TESTING=0
+ 运行编译包的测试 colcon test
+ 允许通过更改src下的部分文件来改变install colcon build --symlink-install

## 创建工作空间和功能包

节点需要存在于功能包当中、功能包需要存在于工作空间当中。所以想创建节点，就要先创建一个工作空间。在创将功能包

### 创建工作空间

创建一个工作空间就是创建一个文件夹

```shell
mkdir -p town_ws/src
cd town_ws/src
```

### 创建一个功能包

创建python版的功能包

```shell
ros2 pkg create village_li --build-type ament_python --dependencies rclpy
```

ament_python 有一个依赖，要依赖rclpy这个库

+ pkg create 创建包
+ --build-type 用来指定该包的编译类型，一共有三个可选项 ament_python、 ament_cmake、 cmake
+ --dependencies 指功能包的依赖，rclpy为ros2的python客户端

```shell
ros2 pkg create pkg01_hello_world_cpp --build-type ament_cmake --dependencies rclcpp --node-name helloworld
```

rclcpp ： ros2 client  ros2客户端 cpp库，其包含了c++ 相关的API  
node-name 自动添加节点源文件名，文件名为helloworld

> 如果build-type 什么都不写的话，ros2默认类型为ament_cmake

### 创建节点文件

现在__init__.py 同级目录下创建一个叫做li4.py的文件

> li4.py 为自定义文件名

#### 添加到CMakeLists.txt

在CMakeLists.txt 最后一行加入下面两行代码

```CMakeList
# 添加源文件
add_executable(node_name path)
# 添加依赖 源文件 编译后要进行链接rclcpp
ament_target_dependencies(node_name rclcpp)
```

然后添加一下代码

```CMakeLists
install(TARGRTS
	node_name
	DESTINATION lib/$(PROJECT_NAME)
)

```

c++版本的ros2节点代码

![](../../../../rescource/Picture/Pasted%20image%2020221221201935.png)

c++ 继承版的ros2节点代码 （建议使用)

![](../../../../rescource/Picture/Pasted%20image%2020221226192517.png)

初始化与资源释放在程序中的作用  
1. 前提：构建的程序可能由若干步骤或阶段组成 => 初始化 --> 节点对象--> 日志输出--> 数据发布-->数据定语--> 资源释放
2. 不同步骤和不同阶段之间涉及数据的传递
3. 怎麼实现数据的传递 => 使用 Context 对象 (上下文对像),这是一个容器，可以存储数据，也可以从中读取数据
4. 初始化其实就是创建Context对象，资源释放就是要销毁Contex对象

>建议通过继承的方式编写节点代码，这样可以在一个进程内创建多个节点，如果采用面向过程的方式编写代码仅能在一个进程内运行一个节点，不好

CMakeLists.txt文件

![](../../../../rescource/Picture/Pasted%20image%2020221221202015.png)

#### 配置文件说明

##### package.xml

该文件包含包名、版本、作者、依赖项的信息，package.xml 可以为colcon构建工具确定包的编译顺序

![](../../../../rescource/Picture/Pasted%20image%2020221226193138.png)

1. 根标签
2. 元信息标签
3. 依赖项

##### CmakeLists.txt

![](../../../../rescource/Picture/Pasted%20image%2020221226194525.png)

## ROS2通信机制-话题与服务

### 话题

Topic 通信模型是一种发布订阅模型

一个节点会创建一个发布者来发布一个话题。另外一个节点会创建一个订阅者，来订阅发布节点的话题

话题通信规则

1. 话题的名字是关键，发布与订阅接口类型要相同
2. 同一个节点可以订阅多个节点，同时也可以发布多个话题
3. 同一个话题可以有多个发布者

### 相关的工具

#### RQT工具 rqt_graph --> 图形化查看节点关系

ROS2在运行过程中，通过命令来查看节点与节点之间的关系

```shell
rqt_graph
# 可通过该命令查看节点与节点之间的关系
```

![rqt_graph](../../../../rescource/Picture/Pasted%20image%2020221221194202.png)

talker 发布 chatter话题 被listener订阅

#### ROS2话题相关CLI工具

##### topic命令

```shell
ros2 topic -h
# 查看topic命令
```

![ros2 topic](../../../../rescource/Picture/Pasted%20image%2020221221194724.png)

```shell
ros2 topic list
# 返回系统值当前活动的所有主题的列表
```

```shell
ros2 topic list -t
#增加消息类型
```

![](../../../../rescource/Picture/Pasted%20image%2020221221195105.png)

```shell
ros2 topic echo topic_name
#打印实时话题内容
```

```shell
ros2 topic info topic_name
#查看主题信息
```

![](../../../../rescource/Picture/Pasted%20image%2020221221200649.png)

publisher count 话题发布者数量
subscription count 话题订阅者数量 

```shell
ros2 interface show 
# 查看消息接口的具体类型
```

![](../../../../rescource/Picture/Pasted%20image%2020221221201051.png)

std_msgs/msg/String为变量名为data的string类型

```shell
ros2 topic pub arg
# 手动发布命令
```


![](../../../../rescource/Picture/Pasted%20image%2020221221201613.png)

### 订阅话题 --> cpp实现

#### 如何编写

1. 创建一个话题订阅者
2. 创建一个话题发布者
3. 获取日志打印 

[rclcpp API查询](https://docs.ros2.org/latest/api/rclcpp/)

获取节点的日志信息
![](../../../../rescource/Picture/Pasted%20image%2020221221203635.png)

创建一个发布者
![](../../../../rescource/Picture/Pasted%20image%2020221221203714.png)

创建并返回一个订阅者
![](../../../../rescource/Picture/Pasted%20image%2020221221203802.png)

创建一个定时器 创建一个客户端
![](../../../../rescource/Picture/Pasted%20image%2020221221203959.png)


#### 代码实现

创建话题订阅者的一般流程

1. 导入订阅的话题接口类型
2. 创建订阅回调函数
3. 声明并创建订阅者
4. 编写订阅回调处理逻辑

