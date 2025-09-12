---
created: 2024-10-23 16:07:58 星期三
modified: 2025-09-12 21:02
tags:
---


[显示键盘按键的网站](https://www.keyboardtester.com/tester.html)：我希望用他来检查哪个按键没有被释放
# 安装
[ubuntu镜像下载](https://launchpad.net/ubuntu/+cdmirrors):选择china南阳理工的镜像源
[Windows11 安装 Ubuntu 避坑指南](https://www.bilibili.com/video/BV1Cc41127B9/?spm_id_from=333.337.search-card.all.click)：只设置根挂载点`/`即可
# 配置
- 查看系统版本：`lsb_release -a`或`cat /etc/issue`
- 同步ubuntu与windows时间
	```shell
	sudo timedatectl set-local-rtc 1
	```
- 在软件和更新ubuntu的软件源：自动选择最佳服务器
- 输入法：拼音模式中启用云输入，常规中启用将每个输入记录新词汇
## 显卡驱动
>注意：bios中设置为显卡为独显直连模式

[ubuntu安装nvidia驱动](https://blog.csdn.net/qq_34972053/article/details/127689332)：直接在系统的附加驱动选择[nvidia-driver-xxx](https://blog.csdn.net/weixin_52828997/article/details/134694963)
[nvidia-smi命令](https://juejin.cn/post/7121912988948234253)
- 查看显卡占用(每秒刷新一次)：`watch -n 1 nvidia-smi`
- 查看显卡频率：`nvidia-smi -q -d CLOCK`
- [卸载显卡驱动](https://zhuanlan.zhihu.com/p/243256494)
## eGPU
- 系统：ubuntu24.10
- 显卡自动识别切换脚本：[all-ways-egpu](https://github.com/ewagner12/all-ways-egpu)，方法2有效。如果不通过这个脚本禁用核显，docker内无法正确调用egpu
```
- [Nvidia-smi really slow to execute](https://forums.developer.nvidia.com/t/nvidia-smi-really-slow-to-execute/165429)：sudo apt install --reinstall nvidia-compute-utils-560
- 查看默认渲染：apt-get install mesa-utils、glxinfo | grep "OpenGL"
```
1. 安装网络工具
```shell
sudo apt install net-tools
```
2. 运行`ifconfig`查本地环回地址
3. 在终端设置环境变量：本地环回地址+端口
```bash
export https_proxy=http://127.0.0.1:7897 && export http_proxy=http://127.0.0.1:7897 && export all_proxy=socks5://127.0.0.1:7897
```
## 个性化配置

### 可选软件包
- ping工具：sudo apt install iputils-ping

### 终端样式
配置文件
├── 文本：120列，24行
├── 颜色：
│   ├── 内置方案：黑底白字
│   ├── 使用透明背景
│   ├── 以亮色显示粗体字

### grub引导配置
- 关机：halt

#### 主题
[grub主题下载](https://www.gnome-look.org/browse/):[Elegant-mountain-grub-themes](https://www.gnome-look.org/p/2206121/)、kawaiki-grub2-theme
- 创建主题目录
```bash
sudo mkdir -p /boot/grub/themes
```
- 移动主题
```bash
sudo mv /path/to/extracted/theme-folder /boot/grub/themes/
```
- 配置主题:
```bash
sudo gedit /etc/default/grub
```
找到 `GRUB_THEME` 行，如果没有这个行，可以添加一行`GRUB_THEME="/boot/grub/themes/theme-folder/theme.txt"`
- 更新grub
```bash
sudo update-grub
```
#### 修改引导菜单
- [如何更改 Ubuntu 中 Grub 启动菜单的屏幕分辨率](https://cn.linux-terminal.com/?p=6550)
- [GRUB 修改启动列表项](https://blog.csdn.net/qq_42127861/article/details/107542981)
- 备份原件：
```bash
sudo cp /boot/grub/grub.cfg /boot/grub/grub.cfg.backup
```
- 编辑配置文件：
```bash
sudo gedit /boot/grub/grub.cfg
```

### gnome扩展
- 安装GNOME Shell扩展
```shell
sudo apt install gnome-shell-extensions
```
[gnome桌面插件配置页面](https://extensions.gnome.org/local/)
- 窗口贴靠工具：Tiling Shell
- 查找鼠标：Wiggle
- 窗口魔灯效果：Compiz alike magic lamp
- 显示应用毛玻璃效果：Blur my Shell ，关闭Dash中Dash to Dock blur
- [调节外接显示器亮度](https://cn.linux-terminal.com/?p=6048#google_vignette)：gnome-display-brightness-ddcutil


应该没用：[UBUNTU NVIDIA使用wayland](https://zhuanlan.zhihu.com/p/383383140)
### 硬件监控
- [硬件占用率及网速显示](https://github.com/fossfreedom/indicator-sysmonitor)：indicator-sysmonitor
```shell
  CPU: {cpu} {cputemp}   GPU:{nvgpu} {nvgputemp}   Memory:{mem}   {simpleNet}  
```
### sunshine
#### 安装
- sunshine：windows{Yama：座机}：
```bash
cd /usr/lib/x86_64-linux-gnu
sudo ln -s libminiupnpc.so.18 libminiupnpc.so.17
dpkg -i install libicu72_72.1-3ubuntu3_amd64.deb
```
#### 配置
1. 打开“启动应用程序”
2. 点击“添加”
3. 在“命令”栏中输入sunshine
4. 点击“保存”，即可在系统启动时自动启动程序
5. 实际配置了`~/.config/autostart`

# 常用命令
## 快捷键
- [截图](https://blog.csdn.net/qq_38880380/article/details/78233687)：`Win + Shift + S` 保存到剪贴板，`Alt + Shift + S` 保存到图片目录
- 打开终端：`Ctrl + Alt + T`
- 关闭终端：`Ctrl + D`或`exit`
- 显示隐藏文件：`Ctrl + H`
- 激活窗口移动模式：`Alt + F7`然后使用键盘的箭头键移动窗口，`enter`键确定，`esc`退出
- 音量调节：`ctrl`+`up/down`
## 命令
### 基本
- 查看当前路径：`pwd`
- 跳到上级目录：`cd ..`
- 进入xx指定目录：`cd xx
- 创建xx文件夹：`mkdir xx`
- 记录终端：
	- 带有颜色控制符：script  -f output.txt开始记录，exit停止记录，使用less -R output.txt查看
	- 纯文本：your_command > output.txt

### 查询
- 列出当前文件夹内容：`ls`, -a显示隐藏文件，-lh以长格式列出更详细信息
- 获取命令使用介绍：`XX --help`
- 显示以 `xx` 开头的命令历史：`history | grep xx`
- 查找名字包含 `xx` 的文件：`sudo find / -name "*xx*"`
- `df -h` ：查看磁盘情况
- `du` ：查看文件夹下各个文件和子文件夹的占用大小
	- `-h`：以人类可读形式(K、M、G)子目录内容
	- `-sh *`：不递归
	- `-sh .*`：不递归显示隐藏文件
	- `sudo du -sh * | sort -hr`： 按文件大小降序排列
- 列出可执行文件或共享库所依赖的动态链接库：`ldd`
- `tree -L n`：打印文件树，L控制深度
### 文件操作
- 创建符号链接： `ln -sf <绝对/相对链接所在目录> <链接名称>`, -s创建链接,-f强制执行,建议使用绝对路径，ls -l查看
- 创建文件：`touch XX`
- 移动文件(夹)：`mv XX /home/y`
- 复制文件并重命名：`cp XX /home/y/YY`
- 删除文件：`rm XX`，删除文件夹：`rm -r XX`
- 下载文件：aria2c -s 16 -c "url"，支持线程和断点
- 压缩：
	```shell
	#压缩文件夹
	sudo tar -czvf archive_name.tar.gz folder_to_compress/
	sudo zip -r myfolder.zip myfolder
	#解压
	sudo tar -czvf version1.tar.gz ./yolokinect
	sudo unzip file_name.zip -d target_path
	```

- [使用vi和vim编辑文件](https://blog.csdn.net/weixin_53269650/article/details/138137434)
- sudo ntfsfix -d /dev/sda：修复硬盘挂载
### 权限相关
- 递归更改文件夹所有者：sudo chown -R yama ~/docker_share/
- 提升操作权限：`sudo`
- 以管理员权限打开文件：`sudo gedit`


# APT工具
apt是基于dpkg的软件包管理工具，可以通过apt、apt-get等命令行工具来使用。dpkg是Debian和基于Debian的Linux（Ubuntu）发行版中的底层软件包管理工具，Debian系统使用.deb作为软件包的文件扩展名
## dpkg
安装deb：`sudo dpkg -i package_name.deb`
查找软件包：`dpkg --list | grep wec`
卸载软件包：`sudo apt --purge remove weixin`
[列出指定软件包的所有文件和安装路径](https://blog.csdn.net/linuxskyer/article/details/135104071)：`dpkg-query -L <package_name>` 命令可以列出指定软件包的所有文件和安装路径
## apt
- apt证书报错： 两种方法
	1. 修改https为http
	2. 安装证书：apt install ca-certificates

- 常用命令
	1. apt update：更新本地软件包列表，读取 `/etc/apt/sources.list` 和 `/etc/apt/sources.list.d/` 目录下的所有文件，获取软件包源的列表
	2. apt install：安装包
	3. apt remove：卸载包
- apt换源：[清华源](https://mirror.tuna.tsinghua.edu.cn/help/ubuntu/)，[阿里源(这个源少包)](https://developer.aliyun.com/mirror/ubuntu)`/etc/apt/sources.list`
	备份：
	```
	sudo cp /etc/apt/sources.list /etc/apt/sources.bak1
	```
参考：
- [apt 和 apt-get的区别以及一些常用命令参考](https://blog.csdn.net/liudsl/article/details/79200134)：两者都是命令行前端工具，推荐使用apt
- 使用`apt`工具安装的包在下载后会被临时存放在`/var/cache/apt/archives`目录下，通常是`.deb`文件
# ssh工具

## 服务端
```shell  
sudo apt install openssh-server
#mkdir /var/run/sshd
passwd #修改密码
# 注意检查gedit /etc/ssh/sshd_config，不要配置重复
echo "Port 22" >> /etc/ssh/sshd_config
echo "PermitRootLogin yes" >> /etc/ssh/sshd_config # 允许root用户SSH登录
echo "PasswordAuthentication yes" >> /etc/ssh/sshd_config #启用密码认证
echo "X11Forwarding yes" >> /etc/ssh/sshd_config #允许X11 GUI 转发
service ssh start
service ssh status
```
## 客户端
```shell
sudo apt install openssh-client
ssh -X root@localhost -p 2222
```

# 环境变量
- 命令：
  1. 列出环境变量：env
  2. 打印环境变量的值：echo $env_name 
  3. 设置环境变量：export env_name=env_value
当你打开一个终端时环境变量主要在以下几个地方被设置：
1. **操作系统级别**：
    - **/etc/environment**：在Linux系统中，这个文件包含了系统级别的环境变量。
    - **/etc/profile**：在某些Linux发行版中，如Ubuntu，这个文件用于设置系统级别的环境变量。
    - **/etc/bash.bashrc**：这个文件包含了bash shell的默认环境变量。
2. **用户级别**：
    - **~/.bashrc**：对于bash shell，这个文件包含了用户级别的环境变量。
    - **~/.bash_profile**：在macOS和一些Linux系统中，这个文件用于设置用户级别的环境变量。
    - **~/.profile**：在某些Linux系统中，这个文件用于设置用户级别的环境变量。
    - **~/.zshrc**：如果你使用的是zsh shell，这个文件将包含用户级别的环境变量。
3. **会话级别**：
    - 当你打开一个新的终端会话时，环境变量也可以在当前会话中被临时设置，例如通过`export`命令。
# else
- [Linux目录配置与FHS标准](https://www.cnblogs.com/antLaddie/p/17613126.html#_label14):[关于usr目录参考](https://www.kawabangga.com/posts/3777):第三方软件安装在/opt目录下
- [需要输入密钥环密码](https://zhuanlan.zhihu.com/p/71924384)：seahorse将login密码设置为空
- [Ubuntu不能挂载移动硬盘问题mounting /dev/sda1](https://blog.csdn.net/qq_27525611/article/details/134363226)
- [调整ubuntu分区大小](https://blog.csdn.net/qq_37071435/article/details/116841152)：`gparted`
- 自说自话的程序将权重下载在哪里？：/root/.cache/
- /dev/shm：是在内存上开辟存储空间存放数据


## 蓝牙wifi等驱动不正常
```shell
yobot@tkbk-ub20:~$ uname -r
6.11.0-26-generic
```

- 通过advanced for ubuntu 进入旧版能上网的内核，然后安装对应内核版本的外设驱动
```bash
sudo apt install linux-modules-extra-$(uname -r)
```
## ubuntu25.04的文件浏览器闪退
- 使用nemo替代：
	```bash
	sudo apt install nemo
	```
	不能卸载nautilus，因为ubuntu与其绑定，卸载会导致未知问题，如文件选择无法使用
在ubuntu中使用MIME类型来识别文件类型，首先查看所有MIME类型及其默认打开应用：
```bash
find /usr/share/mime -name '*.xml' | sed 's|/usr/share/mime/||; s|\.xml$||'  | xargs -I {} sh -c 'echo "{} $(xdg-mime query default {})"'
```
筛选出使用nautilus的文件类型：
```txt
inode/directory org.gnome.Nautilus.desktop
x-content/software nautilus-autorun-software.desktop
x-content/unix-software nautilus-autorun-software.desktop
application/x-compressed-tar org.gnome.Nautilus.desktop
application/gzip org.gnome.Nautilus.desktop
application/x-zstd-compressed-tar org.gnome.Nautilus.desktop
application/zip org.gnome.Nautilus.desktop
application/x-compress org.gnome.Nautilus.desktop
application/x-xz org.gnome.Nautilus.desktop
application/x-tar org.gnome.Nautilus.desktop
application/vnd.rar org.gnome.Nautilus.desktop
application/x-lha org.gnome.Nautilus.desktop
application/zstd org.gnome.Nautilus.desktop
application/x-xar org.gnome.Nautilus.desktop
application/x-lzip org.gnome.Nautilus.desktop
application/x-7z-compressed org.gnome.Nautilus.desktop
application/x-bzip2-compressed-tar org.gnome.Nautilus.desktop
application/x-tarz org.gnome.Nautilus.desktop
application/x-lzma org.gnome.Nautilus.desktop
application/x-lzip-compressed-tar org.gnome.Nautilus.desktop
application/x-xz-compressed-tar org.gnome.Nautilus.desktop
application/x-cpio org.gnome.Nautilus.desktop
application/x-lzma-compressed-tar org.gnome.Nautilus.desktop
```
- 可以查询一个文件类型默认打开的应用如：
```bash
xdg-mime query default application/zip
```

- 将文件夹打开方式切换为nemo，这会修改`~/.config/mimeapps.list`：
	```bash
	xdg-mime default nemo.desktop inode/directory
	```