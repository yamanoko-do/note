---
created: 2024-11-06 20:03:37 星期三
modified: 2024-11-10 12:35:09 星期日
---
# 安装和配置
## docker
- [安装 |Docker 文档](https://docs.docker.com/engine/install/)
- [安装后步骤 |Docker 文档](https://docs.docker.com/engine/install/linux-postinstall/)：[docker命令免sudo](https://www.cnblogs.com/fireblackman/p/16054371.html)
- [docker pull走代理](https://knowledge-things.readthedocs.io/zh-cn/latest/tutorials/docker_pull_proxy.html)：docker拉取镜像的时候，不走系统配置的代理环境，所以需要单独配置它的代理文件,同时代理软件走全局
- [docker镜像仓库](https://hub.docker.com/search?q=ros)
## pull 镜像源
由于国内网络环境，参考[设置镜像拉取源](https://www.cnblogs.com/ikuai/p/18233775)设置国内镜像源：
编辑`/etc/docker/daemon.json`，如：
```json
{
    "registry-mirrors": [
        "https://docker.m.daocloud.io",
        "https://dockerproxy.com",
        "https://docker.mirrors.ustc.edu.cn",
        "https://docker.nju.edu.cn"
    ],
    "runtimes": {
        "nvidia": {
            "args": [],
            "path": "nvidia-container-runtime"
        }
    }
}
```
重启docker服务：
```bash
sudo systemctl daemon-reload && sudo systemctl restart docker
```

## n卡支持
显卡支持nvidia提供了[nvidia-container-tookit安装](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html)使用户能够构建和运行 GPU 加速的容器，若想使用cuda只需要下载nvidia提供的镜像即可
- 创建容器使用：
	1. 使用NVIDIA Container Runtime运行时：`--runtime=nvidia`
	2. 使用宿主机GPU:`--gpus all`
	3. [使用显卡所有功能](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/docker-specialized.html?highlight=nvidia_driver_capabilities)：`--env="NVIDIA_DRIVER_CAPABILITIES=all"`

- [Failed to initialize NVML: Unknown Error解决办法](https://stackoverflow.com/questions/72932940/failed-to-initialize-nvml-unknown-error-in-docker-after-few-hours)：更改`/etc/nvidia-container-runtime/config.toml`中的`no-cgroups = false`


# 命令
- [docker命令大全1](https://www.runoob.com/docker/docker-command-manual.html)
- [docker命令大全2](https://www.zhaowenyu.com/docker-doc/reference/dockercmd/dockercmd-docker-run.html)
## 管理
- 列出镜像：docker image ls
- 删除镜像：docker image rmi，id删除镜像，REPOSITORY:TAG删除索引
- [创建容器](https://www.cnblogs.com/happy4java/p/11206853.html)：--rm（退出时删除容器）
- 显示全部容器：docker ps -a 
- 启动容器：docker start
- 进入容器：docker exec -it ID bash
- 查看容器资源状态：docker stats
- 容器迁移(容器复制)：[docker commit指令和docker export指令有什么区别](https://blog.csdn.net/Dontla/article/details/122958994):先commit保存容器为镜像，然后save和load保存和加载镜像
	```shell
	docker commit container_id REPOSITORY:TAG #保存容器为镜像
	docker save -o myimage.tar REPOSITORY:TAG
	docker load -i myimage.tar
	#重新加载可能出现名字可能为none，重命名镜像
	docker tag iamgeid repository:tag
	```

## 清理
docker会导致`/var/lib/docker/overlay2`占用量爆长，通过`sudo du -h --max-depth=1 /var/lib/docker/overlay2`查看该目录的具体占用，然和显示的实际值与`docker system df`不一致，overlay2存在一些层无法被docker检测，就好像他们与docker无关一样，需要手动删除。

- 目前清理方式：保留有用的镜像到tar，然后执行清理
- 删除关闭的容器、无用的数据卷和网络，以及无tag的镜像：
```bash
docker system prune
```
- 将没有容器使用 Docker 镜像都删掉：**谨慎使用，备份镜像**
```bash
docker system prune -a
```
- 查看overlay并手动删除

# docker的网络
[docker的网络模式](https://www.bilibili.com/video/BV1Aj411r71b/?spm_id_from=333.880.my_history.page.click&vd_source=ca17e8e44a1bddd49f39d4bc7c4ad336)

- 观察到不使用--network=host，容器内的网络会（慢或是有延时，不确定是哪个，可能是中间的转接层增加了处理时间？）

安装docker时，宿主机上同时会安装一个[虚拟的docker0网桥](https://segmentfault.com/a/1190000044982699)。使用默认的bridge模式启动docker容器时，docker就会给容器分配一个ip，在容器外部可以使用该ip进行ssh的远程连接，容器内可以通过docker0的地址访问主机上的网络
1.  [vscode通过ssh远程连接容器进行开发](https://www.bilibili.com/video/BV1tW4y1E74Z/?spm_id_from=333.1007.top_right_bar_window_history.content.click)
2. 容器内走宿主机代理
   - Clash设置allowLAN
   - 环境变量设置到docker0的地址

- 测试：`wget www.google.com`

``` bash
export https_proxy=http://172.17.0.1:7897;export http_proxy=http://172.17.0.1:7897;export all_proxy=socks5://172.17.0.1:7897
```

# docker的bug
- appimage程序无法在容器中正常运行


# 构建镜像
构建镜像的目的是提供一个能够复用的环境，由于高速的网络不是随时待命的，我放弃了学习dockerfile的方式，选择容器commit为镜像的方式，并且将代码、和conda挂载在外面，里记录一些步骤和常用软件包：
1. 首先在[nvidia/cuda](https://hub.docker.com/r/nvidia/cuda)仓库选择需要的镜像：选择devel和ubuntu
	- base(基本运行环境，无法编译开发)、runtime(更全面的运行环境，无法编译开发)、devel（最全，可编译）：
	- 系统：ubuntu、centos等
	- cudnn
2. apt换源
3. 安装一些包，列一些我能想到的：
	1. apt install iputils-ping：ping
	2. net-tools：ifconfig
	3. iproute2：ip address
	4. git
	5. gedit
	6. wget
	7. tree
	8. curl
4. 安装conda或在`~/.bashrc`中索引conda，并[配置conda源](python.md#channel)
	```bash
	# >>> conda initialize >>>
	# !! Contents within this block are managed by 'conda init' !!
	__conda_setup="$('/root/host_share/miniconda3/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
	if [ $? -eq 0 ]; then
	    eval "$__conda_setup"
	else
	    if [ -f "/root/host_share/miniconda3/etc/profile.d/conda.sh" ]; then
	        . "/root/host_share/miniconda3/etc/profile.d/conda.sh"
	    else
	        export PATH="/root/host_share/miniconda3/bin:$PATH"
	    fi
	fi
	unset __conda_setup
	# <<< conda initialize <<<
	```
5. 存成镜像

# 运行模板

``` bash
docker run --privileged -itd \
  --name langrasp \
  --runtime=nvidia \
  --gpus all \
  -m 8g \
  --memory-swap=16g \
  --shm-size 8g \
  --cpus 8\
  -e DISPLAY=:0 \
  -e NVIDIA_DRIVER_CAPABILITIES=all \
  -v /tmp/.X11-unix:/tmp/.X11-unix \
  -v /home/yama/docker_share:/root/host_share \
  -v /var/run/dbus/system_bus_socket:/var/run/dbus/system_bus_socket \
36c
```

## 容器资源
### 内存
`-m`：限制容器可使用的内存上限：这包括应用、堆栈、共享内存等所有内存，默认是宿主内存上限
`--shm-size`：限制容器共享内存上限，默认64M
`--memory-swap`：内存和swap的总大小，未指定时，相当于两倍-m大小，也就是swap=m
```bash
df -h /dev/shm # 在容器中查看共享内存
cat /sys/fs/cgroup/memory.max # 查看内存大小
cat /sys/fs/cgroup/memory.swap.max # 查看swap大小

mount -o remount,size=16G /dev/shm # 临时调整共享内存为16G
```
### cpu
- nproc：查看宿主机cpu核心数
`--cpus`：总 CPU 时间额度限制，使用cat /sys/fs/cgroup/cpu.max查看每个调度周期可使用的cpu时间
`--cpuset-cpus`：固定使用的cpu核心id，使用cat /sys/fs/cgroup/cpuset.cpu查看允许使用的核心id
### 数据卷
[避免docker挂载时产生root权限文件](https://geofftools.cn/blog/mount-docker-without-creating-root-file/)：还是没有一个好的方式
## 容器内的GUI显示
[Docker容器显示图形到宿主机屏幕](https://blog.csdn.net/Frank_Abagnale/article/details/80243939?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-3.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-3.control)
### 挂载方式
- `-v /tmp/.X11-unix:/tmp/.X11-unix`：共享本地unix端口
- `-e DISPLAY=:0`,`-e DISPLAY=unix:0有些程序报qt相关的错`
- 宿主机运行xhost +
### 网络方式
shh 配置的X11转发很不稳定，帧率较低
1. 首先配置[ssh](ubuntu.md#ssh工具)
2. 根据cat /etc/hosts和xauth list设置服务端的DISPLAY变量如：(X11转发方式)
	```shell
	root@961c4c78fbcd:~# cat /etc/hosts
	127.0.0.1	localhost #确认localhost的ip
	::1	localhost ip6-localhost ip6-loopback
	fe00::	ip6-localnet
	ff00::	ip6-mcastprefix
	ff02::1	ip6-allnodes
	ff02::2	ip6-allrouters
	172.17.0.3	961c4c78fbcd
	root@961c4c78fbcd:~# xauth list
	961c4c78fbcd/unix:10  MIT-MAGIC-COOKIE-1  46491779e0b4db6a112b29064b0e91c2 #这里看到是10
	
	export DISPLAY=127.0.0.1:10
	
	apt install xarclock
	xarclock
	```

## 例子

```shell
docker run 
  --name rl_arm \
  -m 16g \ #内存
  --shm-size 8g \ #共享内存
  --cpus 2\ #2核算力
  --cpuset-cpus=0,1 \ #使用1-2核心 
  -e DISPLAY=:0 \
  -v /tmp/.X11-unix:/tmp/.X11-unix \
  -v /home/yama/docker_share:/root/host_share \
  -v /var/run/dbus/system_bus_socket:/var/run/dbus/system_bus_socket \
681
```
