## 背景

我们都知道Docker是一个开源项目，提供了一个打包、分发和运行任意程序的轻量级[容器](https://cloud.tencent.com/product/tke?from_column=20065&from=20065)的开放平台。它没有语言 支持、框架或者打包系统的限制，并可以运行在任何地方、任何时候，从小型的家用电脑到高端的[服务器](https://cloud.tencent.com/act/pro/promotion-cvm?from_column=20065&from=20065)都可以运行。这让人们可以打包不同的包用于部署和扩展网 络应用，[数据库](https://cloud.tencent.com/solution/database?from_column=20065&from=20065)和后端服务而不必依赖于特定的栈或者提供商。

但是docker的应用环境不止限于web等不需要GUI的场景，如果我们需要经常测试新的框架，或者在本地开发一个docker image，随后上传到服务器。由于没有了软件环境依赖的麻烦，一切都显得非常方便。

为了演示如何在docker中运行GUI程序，我们以firefox为例。

以下所有代码的环境为`ubuntu 16.04 amd64`, 其他发行版可进行适当修改。

## 步骤

- 安装docker

```bash
sudo apt install docker.io 
```

- 拉取一个image

```bash
docker pull ubuntu:16.04 
```

- 运行一个容器

```bash
docker run -ti --net=host --rm -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix 
```

- 安装firefox

```bash
apt install firefox 
```

- 运行firefox

```bash
firefox 
```

然后你就能从host的桌面看到firefox了。

## 其他问题

- 权限错误

你可能会看到如下错误：

```bash
No protocol specified
No protocol specified
No protocol specified
No protocol specified
```

这是由于X11服务默认只允许**来自本地的用户**启动的图形程序将图形显示在当前屏幕上。

解决的办法很简单，允许所有用户访问X11服务即可。这个事情可以用xhost命令完成。

```bash
sudo apt-get install x11-xserver-utils
xhost +
# 参数『+』表示允许任意来源的用户
```

- 软件未安装错误

虽然可以看到界面，但是docker命令行会提示一些错误。如下：

![](../../../resources/ad0b599b1f2801bff86665a5f83ea7a1)

运行以下命令可以解决：

```bash
apt install dbus-x11 
apt-get install libcanberra-gtk3-module
```