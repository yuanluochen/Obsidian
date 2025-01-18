# RobotMaster官方代码分析

## 底盘任务

### 变量

#### 枚举类型 enum

##### chassis_mode_e

  ```c
  typedef enum
{
  CHASSIS_VECTOR_FOLLOW_GIMBAL_YAW,
  CHASSIS_VECTOR_FOLLOW_CHASSIS_YAW,
  CHASSIS_VECTOR_NO_FOLLOW_YAW,
  CHASSIS_VECTOR_RAW,

  //  CHASSIS_AUTO,
  //  CHASSIS_FOLLOW_YAW,
  //  CHASSIS_ENCODER,
  //  CHASSIS_NO_ACTION,
  //  CHASSIS_RELAX,
} chassis_mode_e;
  ```

该枚举为存放底盘运动状态枚举类型

#### 结构体 struct

##### Chassis_Motor_t

```c
typedef struct

{

  const motor_measure_t *chassis_motor_measure;

  fp32 accel;

  fp32 speed;

  fp32 speed_set;

  int16_t give_current;

} Chassis_Motor_t;

```

作用未知

##### chassis_move_t

```c
typedef struct

{

  const RC_ctrl_t *chassis_RC;               //底盘使用的遥控器指针

  const Gimbal_Motor_t *chassis_yaw_motor;   //底盘使用到yaw云台电机的相对角度来计算底盘的欧拉角

  const Gimbal_Motor_t *chassis_pitch_motor; //底盘使用到pitch云台电机的相对角度来计算底盘的欧拉角

  const fp32 *chassis_INS_angle;             //获取陀螺仪解算出的欧拉角指针

  chassis_mode_e chassis_mode;               //底盘控制状态机

  chassis_mode_e last_chassis_mode;          //底盘上次控制状态机

  Chassis_Motor_t motor_chassis[4];          //底盘电机数据

  PidTypeDef motor_speed_pid[4];             //底盘电机速度pid

  PidTypeDef chassis_angle_pid;              //底盘跟随角度pid

  

  first_order_filter_type_t chassis_cmd_slow_set_vx;

  first_order_filter_type_t chassis_cmd_slow_set_vy;

  

  fp32 vx;                         //底盘速度 前进方向 前为正，单位 m/s

  fp32 vy;                         //底盘速度 左右方向 左为正  单位 m/s

  fp32 wz;                         //底盘旋转角速度，逆时针为正 单位 rad/s

  fp32 vx_set;                     //底盘设定速度 前进方向 前为正，单位 m/s

  fp32 vy_set;                     //底盘设定速度 左右方向 左为正，单位 m/s

  fp32 wz_set;                     //底盘设定旋转角速度，逆时针为正 单位 rad/s

  fp32 chassis_relative_angle;     //底盘与云台的相对角度，单位 rad/s

  fp32 chassis_relative_angle_set; //设置相对云台控制角度

  fp32 chassis_yaw_set;

  fp32 vx_max_speed;  //前进方向最大速度 单位m/s

  fp32 vx_min_speed;  //前进方向最小速度 单位m/s

  fp32 vy_max_speed;  //左右方向最大速度 单位m/s

  fp32 vy_min_speed;  //左右方向最小速度 单位m/s

  fp32 chassis_yaw;   //陀螺仪和云台电机叠加的yaw角度

  fp32 chassis_pitch; //陀螺仪和云台电机叠加的pitch角度

  fp32 chassis_roll;  //陀螺仪和云台电机叠加的roll角度

  

} chassis_move_t;
```

该结构体为控制底盘运动状态的结构体

### 任务函数

#### 初始化部分

##### 延时 357

```c
//空闲一段时间
vTaskDelay(CHASSIS_[[TASK](../../../RobotMaster/飞镖/Task.md)](../../../RobotMaster/飞镖/Task.md)_INIT_TIME); 
```

此延时函数中的参数 CHASSIS_TASK_INIT_TIME 为定义的宏

```c
#define CHASSIS_TASK_INIT_TIME 357
```

##### 底盘初始化

```c
chassis_init(&chassis_move);
```

该初始化函数参数为 [[DJI官方代码分析#chassis_move_t|chassis_move_t]]* 型的结构体指针变量,

- 验证传入结构体参数是否为空

```c
if (chassis_move_init == null)
{
  return;
}
```

- 设置PID [[../../../博客/PID控制算法概述及算法分析/PID控制算法概述及算法分析#^aa51c7|Kp]]，[[../../../博客/PID控制算法概述及算法分析/PID控制算法概述及算法分析#^1f2e6d|Ki]], [[../../../博客/PID控制算法概述及算法分析/PID控制算法概述及算法分析#^f56bbe|Kd]]数值，其用于后期PID初始化

```c

//底盘速度环pid值

const static fp32 motor_speed_pid[3] = {M3505_MOTOR_SPEED_PID_KP, M3505_MOTOR_SPEED_PID_KI, M3505_MOTOR_SPEED_PID_KD};

//底盘旋转环pid值

const static fp32 chassis_yaw_pid[3] = {CHASSIS_FOLLOW_GIMBAL_PID_KP, CHASSIS_FOLLOW_GIMBAL_PID_KI, CHASSIS_FOLLOW_GIMBAL_PID_KD};

const static fp32 chassis_x_order_filter[1] = {CHASSIS_ACCEL_X_NUM};

const static fp32 chassis_y_order_filter[1] = {CHASSIS_ACCEL_Y_NUM};
```

- 对参数结构体指针所指向的结构体进行赋值进行赋值

```c
//底盘开机状态为停止
chassis_move_init->chassis_mode = CHASSIS_VECTOR_RAW;
//获取遥控器指针
chassis_move_init->chassis_RC = get_remote_control_print();
//获取陀螺仪姿态角指针
chassis_move_init->chassis_INS_angle = get_INS_angle_print();
//获取云台电机数据指针
chassis_move_init->chassis_yaw_motor = get_yaw_motor_point();
chassis_move_init->chassis_pitch_motor = get_pitch_motor_point();
```

- 初始化[[../../../博客/PID控制算法概述及算法分析/PID控制算法概述及算法分析|PID]]

```c
//初始化PID 运动
for (i = 0; i < 4; i++)
{
  chassis_move_init->motor_chassis[i].chassis_motor_measure = get_Chassis_Motor_Measure_Point(i);

  PID_Init(&chassis_move_init->motor_speed_pid[i], PID_POSITION, motor_speed_pid, M3505_MOTOR_SPEED_PID_MAX_OUT, M3505_MOTOR_SPEED_PID_MAX_IOUT);
}
//初始化旋转PID
PID_Init(&chassis_move_init->chassis_angle_pid, PID_POSITION, chassis_yaw_pid, CHASSIS_FOLLOW_GIMBAL_PID_MAX_OUT, CHASSIS_FOLLOW_GIMBAL_PID_MAX_IOUT);
```

- 此段无法理解

```c
//用一阶滤波代替斜波函数生成
first_order_filter_init(&chassis_move_init->chassis_cmd_slow_set_vx, CHASSIS_CONTROL_TIME, chassis_x_order_filter);
first_order_filter_init(&chassis_move_init->chassis_cmd_slow_set_vy, CHASSIS_CONTROL_TIME, chassis_y_order_filter);
```

- 对底盘电机速度进行限制

```c
//最大 最小速度
chassis_move_init->vx_max_speed = NORMAL_MAX_CHASSIS_SPEED_X;
chassis_move_init->vx_min_speed = -NORMAL_MAX_CHASSIS_SPEED_X;
chassis_move_init->vy_max_speed = NORMAL_MAX_CHASSIS_SPEED_Y;
chassis_move_init->vy_min_speed = -NORMAL_MAX_CHASSIS_SPEED_Y;
```

- 更新chassus_move_t结构体其他成员变量的数值

```c
//更新一下数据
chassis_feedback_update(chassis_move_init);
```

##### 保证底盘电机在线

若电机不在线，则反复循环，直至在线为止，具体实行现无法理解

```c
while(toe_is_err(ChassisMotor1TOE) || toe_is_err(ChassisMotor2TOE) || toe_is_err(ChassisMotor3TOE) || toe_is_err(ChassisMotor4TOE) || toe_os_error(DBUSTOE))
{
	vTaskDelay(CHASSIS_CONTROL_TIME_MS);
}
```

CHASSIS_CONTROL_TIME_MS为宏定义

```c
//底盘任务控制间隔 2ms
#define CHASSIS_CONTROL_TIME_MS 2
```

while循环的判断部分的函数的参数为定义的枚举类型，这个枚举类型存储设备的错误码

```c
//错误码以及对应设备顺序
enum errorList
{
    DBUSTOE = 0,
    YawGimbalMotorTOE,
    PitchGimbalMotorTOE,
    TriggerMotorTOE,
    ChassisMotor1TOE,
    ChassisMotor2TOE,
    ChassisMotor3TOE,
    ChassisMotor4TOE,
    errorListLength,
};
```

#### 任务循环部分

##### 获取遥控器开关数据，设置底盘状态

```c
//遥控器设置状态
chassis_set_mode(&chassis_move);
```

上述函数实现如下

```c
static void chassis_set_mode(chassis_move_t *chassis_move_mode)
{
	/*
	** 验证传入指针是否为空
	*/
    if (chassis_move_mode == NULL)
    {
        return;
    }
    chassis_behaviour_mode_set(chassis_move_mode);
}
```
