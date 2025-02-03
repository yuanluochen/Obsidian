终端报错如下
```
yuanluochen@192 ~> google-chrome  
[6109:6109:0113/222309.177139:ERROR:process_singleton_posix.cc(358)] 其他计算机 (anonymous) 的另一个 Goo  
gle Chrome 进程 (6396) 好像正在使用此个人资料。Chrome 已锁定此个人资料以防止其受损。如果您确定其他进程目o  
前未使用此个人资料，请为其解锁并重新启动 Chrome。h  
[6109:6109:0113/222309.177203:ERROR:message_box_dialog.cc(190)] Unable to show message box: Google Chrom  
e - 其他计算机 (anonymous) 的另一个 Google Chrome 进程 (6396) 好像正在使用此个人资料。Chrome 已锁定此个o  
人资料以防止其受损。如果您确定其他进程目前未使用此个人资料，请为其解锁并重新启动 Chrome。r
```

问题出现源自于切换了一次用户

## 解决方案

![](../../../rescource/Attachment/Pasted%20image%2020250113224947.png)

```shell
cd ~/.config
cd google-chrome
rm Singleton*
# 打开软件
```
