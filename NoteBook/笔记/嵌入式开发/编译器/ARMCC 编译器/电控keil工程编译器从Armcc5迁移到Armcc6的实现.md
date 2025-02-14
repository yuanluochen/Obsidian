
# 参考文献

https://www.cnblogs.com/CodeWorkerLiMing/p/12034471.html

# 主要步骤

**第一步**，替换编译器为ac6及以上

![](../../../../rescource/Attachment/Pasted%20image%2020250214135657.png)

也可

![](../../../../rescource/Attachment/Pasted%20image%2020250214140031.png)

**第二步**，更改编译宏定义，去除__CC_ARM

![](../../../../rescource/Attachment/Pasted%20image%2020250214135716.png)

也可

![](../../../../rescource/Attachment/Pasted%20image%2020250214140238.png)

**第三步**，替换系统文件，主要需要替换文件为Middleware文件，替换为AC6工程内的文件

![](../../../../rescource/Attachment/Pasted%20image%2020250214135748.png)

**第四步**，修改一些AC5版本代码的语法，主要修改__packed,将其替换为__**attribute__**((packed))

![](../../../../rescource/Attachment/Pasted%20image%2020250214141536.png)
```c
typedef __packed struct
typedef struct __attribute__((packed))
```


**最后**，编译代码，修改一些warning