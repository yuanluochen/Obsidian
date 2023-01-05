# 山东理工大学机器人擂台赛培训

**Arduino 入门**

![](电控组+视觉组/attachment/Pasted%20image%2020221118231514.png)

---

[太极创客](http://www.taichi-maker.com/)

[Arduino官方网站](https://www.arduino.cc/)

---

**教学用具**

- Arduino开发板
- 面包板
- LED灯，电阻，两根公对公头的杜邦线

---

## Arduino开发板的使用

- 获取Arduino IDE
- Arduino IDE使用简介

---

### 利用Arduino IDE编写程序

---

**Arduino 程序结构**

![](电控组+视觉组/attachment/Pasted%20image%2020221119005147.png)

---

**C语言版本Arduino程序**

```c
#include <stdio.h>
int main()
{
	//程序硬件配置程序
	setup();
	while(1)
	{
		//硬件运行程序
		loop();
	}
	return 0;
}
```

---

## 点亮LED

---

### 面包板的使用

---

![面包板](电控组+视觉组/attachment/Pasted%20image%2020221119011645.png)

---

### LED灯

LED 发光二极管

![LED](电控组+视觉组/attachment/LED%201.png)

<!-- 电流从LED正极流入，从负极流出，电阻趋近零，LED灯亮，相反，电阻无穷大，LED不亮，所以如果我们想点亮LED灯 ，我们可以控制开发板发出电流，来驱动LED灯亮-->

---

LED电路图

![LED电路图](电控组+视觉组/attachment/Pasted%20image%2020221119030831.png)

---

[Arduino点亮LED灯的函数资料](http://www.taichi-maker.com/homepage/reference-index/arduino-code-reference/)

![Arduino开发板](电控组+视觉组/attachment/Pasted%20image%2020221119151758.png)

---

**Arduino点亮LED灯**

```c
void setup()
{
  // put your setup code here, to run once:
  pinMode(2, OUTPUT);//配置LED为输出模式
}
void loop()
{
  // put your main code here, to run repeatedly:
  digitalWrite(2, HIGH);//LED 开

   }
```


