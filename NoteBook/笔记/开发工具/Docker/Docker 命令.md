# Docker 命令

Docker 必须部署在linux内核的环境下

## Docker常用操作命令

### 帮助启动命令

+ 启动docker : systemctl start docker
+ 停止docker : systemctl stop docker
+ 重启docker : systemctl restart docker
+ 查看docker状态 : systemctl status docker
+ 开机启动 : systemctl enable docker
+ 查看docker概要信息 : docker info
+ 查看docker总体帮助文档 : **docker --help**
+ 查看docker命令帮助文档 : docker 具体命令 --help

### 镜像命令

+ ==docker images : 列出本地主机上的镜像==
	+ 参数说明
		+  REPOSITORY : 表示镜像的仓库源
		+ TAG : 镜像标签，版本号
		+ IMAGE ID : 镜像ID
		+ CREATED : 镜像创建时间
		+ SIZE : 镜像大小
		> 同一个仓库源可以有多个TAG版本，代表这个仓库源的不同个版本，我们使用REPOSITORY:TAG 来定义不同的镜像
	+ OPTIONS说明
		+  -a : 列出本地所有的镜像 （含历史映像层）
		+  -q : 只显示镜像ID
+ ==docker serach 某个XXX镜像名字 : 搜索某个镜像是否在远程仓库==
	+ 网站 https://hub.docker.com
	+ 命令
		+ docker search [OPTIONS] 镜像名字
		+ 案例 ![案例](../../../resources/Pasted%20image%2020221009122958.png)
			+ 参数说明
				+ NAME 镜像名字
				+ DESCRIPTION 镜像说明
				+ STARS 点赞数量
				+ OFFICAL 是否是官方的
				+ AUTOMATED是否是自动构建的
	+ OPTIONS 说明
		+ --limit : 只列出 N 个镜像，默认25个
		+ docker search --limit 5 redis
+ ==docker pull 某个XXX镜像名字 : 下载镜像==
	+ docker pull 镜像名字 [: TAG]
	+ docker pull 镜像名字 
		+ 没有TAG就是最新版，其等价于 docker pull 镜像名字 : latest
+ ==docker system df 查看镜像/容器/数据卷所占的空间==
+ docker rmi 某个XXX镜像名字ID ： 删除镜像
	+ 删除镜像
	+ 删除单个 docker rmi -f 镜像ID
	+ 删除多个 docker rmi -f 镜像名1:TAG 镜像名2:TAG
	+ 删除全部 docker rmi -f $(docker images -qa)

### 容器命令

+ ==新建+启动容器 docker run [OPTIONS] IMAGE [COMMAND] [ARG]==
	+ OPENTIONS说明
		+ --name="容器新名字" 为容器指定一个名称
		+ -d: 后台运行容器并返回容器ID，也即启动守护式容器（后台运行）
		+ -i: 以交互模式运行容器，通常与-t同时使用 (i interavtive)
		+ -t: 为容器重新分配一个伪输入终端，通常与-i同时使用 (t tty 伪终端)
		+ -it: 启动交互式容器，启动以后有进一步的命令输入请求，并且返回一个终端
		+ -P: 随机端口映射，大写P
		+ -p: 指定端口映射，小写p
+ ==列出当前所有正在运行的容器 docker ps [OPTIONS]==
+ ==退出容器==
	+ exit run进去容器，exit退出，容器停止
	+ ctrl + p + q run进去容器，ctrl+p+q退出，容器不停止
+ ==启动已经停止运行的容器 : docker start 容器ID或容器名==
+ ==重启容器 docker restart 容器ID或容器名==
+ ==停止容器 docker stop 容器ID或容器名==
+ ==强制停止容器 docker kill 容器ID或容器名==
+ ==删除已停止的容器 docker rm 容器ID==
+ ==重新进入容器 docker exec [OPTIONS] 容器ID [ARG]==
> 重要:docker容器后台运行时必须有一个前台进程


+ ==docker 封装镜像指令  ==
```bash
docker commit -m="镜像信息" -a="作者/yuanluochen" 镜像ID(通过docker ps查看) 要创建的目标容器名:[标签名]
#example
docker commit -m="add rm vision code and it can build in this images" -a="yuanluochen" 909e4c9dca1e yuanluochen/machine-vision:1.0
# 目标容器名要求要小写
```
+ ==docker image发布到dockerhub==
首先在dockerhub上创建镜像仓库![](../../../resources/Pasted%20image%2020250117144514.png)
![](../../../resources/Pasted%20image%2020250117144554.png)镜像名要与仓库名同名
```bash
docker tag local-image:tagname new-repo:tagname#同名就不用了
docker push new-repo:tagname#主要是这个
```

### Docker 配置问题
#### vscode 相关 ![](../../../resources/Pasted%20image%2020250113220347.png) vscode有如下报错主要是vscode默认不是用root权限进行使用的，无权访问docker

```
因为你的用户没有足够的权限来访问Docker守护进程的socket文件，这通常需要管理员或具有特定权限的用户来执行Docker操作：
（1）将你的用户添加到 `docker` 用户组中：
sudo usermod -aG docker $USER
（2）重新登录你的帐户，以便用户组更改生效
（3）查看镜像：
docker images
```

==注意要重启，可能很晚才能生效

#### docker 里面没有sudo
```
apt-get install sudo
```