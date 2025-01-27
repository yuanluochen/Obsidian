参考链接
https://sdutvincirobot.feishu.cn/wiki/KRSMwKmTvivWRskSRszc2vfNnoc?from=from_copylink
构建容器按如下方式构建--带nvidia驱动
```bash
sudo docker run -dit \
--gpus all \
--name=contianer_name \
--privileged \
-e NVIDIA_DRIVER_CAPABILITIES=all \
-e DISPLAY=$DISPLAY \
-e WAYLAND_DISPLAY=$WAYLAND_DISPLAY \
-e XDG_RUNTIME_DIR=$XDG_RUNTIME_DIR \
-e QT_QPA_PLATFORM=wayland \
-v /home/yuanluochen:/home/yuanluochen \
-v /tmp/.X11-unix:/tmp/.X11-unix:rw \
-v /run/user/$(id -u)/wayland-0:/run/user/1000/wayland-0:rw \
-v /dev/dri:/dev/dri \
--device=/dev/snd \
--device=/dev/dri/renderD128 \
--net=host \
-w /home/yuanluochen \
#docker_image name
```
允许X11访问： 在主机上运行以下命令以允许X11访问：

```Bash
xhost +local:docker
```