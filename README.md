# `ArchLinux` 安装教程
[![GitHub issues](https://img.shields.io/github/issues/ds19991999/ArchLinux-Install-Tutorial.svg)](https://github.com/ds19991999/ArchLinux-Install-Tutorial/issues)
[![Github downloads](https://img.shields.io/github/downloads/ds19991999/ds19991999.github.io/total.svg)](https://github.com/ds19991999/ArchLinux-Install-Tutorial/releases/latest)
[![DUB](https://img.shields.io/dub/l/vibe-d.svg)](https://github.com/ds19991999/ArchLinux-Install-Tutorial/blob/master/LICENSE)
[![GitHub release](https://img.shields.io/github/release/ds19991999/ds19991999.github.io.svg)](https://github.com/ds19991999/ArchLinux-Install-Tutorial/releases)
[![Badge](https://img.shields.io/badge/link-996.icu-red.svg)](https://996.icu/#/zh_CN)

可能是最详细的`ArchLinux`中文安装攻略。

说明：`ArchLinux`号称最难上手的`Linux`操作系统, 它采用滚动式升级，还能回滚，这是其他大部分操作系统无法比拟的。以前读书的时候搞过几次，每次都是因为学校那个恶心的网络限制无法正常安装，反正就是无法联网，而`ArchLinux`是联网安装的，所以在安装之前请确保你的网络正常，没有`IP`和`MAC`地址限制。

> 官方中文wiki：https://wiki.archlinux.org/index.php/Arch_Linux_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)
>
> 安装指导：[Installation guide](https://wiki.archlinux.org/index.php/Installation_guide)



![](https://raw.githubusercontent.com/ds19991999/image/master/picgo/20190706164733.jpg)

## 目录

* [1. 准备工作](#准备工作)
* [2. 创建虚拟机](#创建虚拟机)
* [3. 进入临时系统](#进入临时系统)
* [4. 开始安装系统](#开始安装系统)
  * [4.1.  联网更新时间](#联网更新时间)
  * [4.2. 分区](#分区)
  * [4.3. 安装基本系统](#安装基本系统)
  * [4.4. 配置](#配置)
* [5. 安装桌面](#安装桌面)
  * [5.1. 重启进入系统](#重启进入系统)
  * [5.2. 安装前准备](#安装前准备)
  * [5.3. 配置DNS](#配置dns)
  * [5.4. 安装桌面](#安装桌面)
  * [5.5. 配置桌面网络](#配置桌面网络)
* [6. 进入桌面](#进入桌面)
* [7. 安装软件](#安装软件)
  * [7.1. 安装`vmware-tools`](#安装`vmware-tools`)
  * [7.2. 安装中文输入法](#安装中文输入法)
  * [7.3. 正式修改软件源](#正式修改软件源)
  * [7.4. 安装chromium](#安装chromium)
  * [7.5. 安装pip](#安装pip)
  * [7.6. 安装musicbox](#安装musicbox)
  * [7.7. 无道词典](#无道词典)

* [8. 美化](#美化)
  * [8.1. 终端配色](#终端配色)

* [9. 代理使用](#代理使用)
* [10. 结语](#结语)

## 准备工作

* archlinux官方镜像下载：https://www.archlinux.org/download/
* `VMware-15.1.0-zh-cn-Pro-Crack`：http://www.carrotchou.blog/122.html

## 创建虚拟机

![](http://image.creat.kim/picgo/20190705235035.png)

![](http://image.creat.kim/picgo/20190705235133.png)

![](http://image.creat.kim/picgo/20190705235158.png)

![](http://image.creat.kim/picgo/20190705235252.png)

![](http://image.creat.kim/picgo/20190705235450.png)



![](http://image.creat.kim/picgo/20190705235739.png)

这里一定要选`UEFI`

![](http://image.creat.kim/picgo/20190706075834.png)

![](http://image.creat.kim/picgo/20190705235808.png)

## 进入临时系统

![](http://image.creat.kim/picgo/20190706080050.png)

![](http://image.creat.kim/picgo/20190706000115.png)

## 开始安装系统

### 联网更新时间

```shell
dhcpcd
ping -c 4 baidu.com
```

![](http://image.creat.kim/picgo/20190706000449.png)

```shell
timedatectl set-ntp true
```

![](http://image.creat.kim/picgo/20190706000616.png)

### 分区

```
fdisk -l
```

![](http://image.creat.kim/picgo/20190706000743.png)

这里对`/dev/sda`进行分区

```
fdisk /dev/sda
g
n
1
+200M
n
p
```

![](http://image.creat.kim/picgo/20190706001335.png)

```
t
1
1
w
```

![](http://image.creat.kim/picgo/20190706001524.png)

格式化`esp`分区和根分区

```shell
mkfs.fat -F32 /dev/sda1
mkfs.ext4 /dev/sda2
```

挂载根分区

```shell
mount /dev/sda2 /mnt
```

挂载`esp`分区

```shell
mkdir /mnt/boot
mount /dev/sda1 /mnt/boot
```

![](http://image.creat.kim/picgo/20190706002020.png)

### 安装基本系统

改软件源

```shell
nano /etc/pacman.d/mirrorlist
```

光标移到`China`一行，`Ctrl+K`剪切，回到最前面，`Ctrl+U`粘贴，`Ctrl+X`退出，输入`y`回车保存。

安装基础包

```shell
pacstrap /mnt base base-devel
```

等几分钟就安装完了。

### 配置

![](http://image.creat.kim/picgo/20190706003559.png)

```shell
# 生成自动挂载分区的 fstab 文件
genfstab -L /mnt >> /mnt/etc/fstab
cat /mnt/etc/fstab

# 切换到新系统
arch-chroot /mnt

# 设置时区（上海）
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

# 根据硬件调整时间
hwclock --systohc

# 本地化编辑
nano /etc/locale.gen
# 将下面两行注释取消
en_US.UTF-8 UTF-8
zh_CN.UTF-8 UTF-8
zh_TW.UTF-8 UTF-8

# 生成locale讯息
locale-gen

# 设置默认语言为英文
nano /etc/locale.conf
LANG=en_US.UTF-8

# 安装vm
pacman -S vim

# 设置主机名
vim /etc/hostname
arch

# 编辑hosts
vim /etc/hosts
127.0.0.1 localhost.localdomain localhost
::1 localhost.localdomain localhost
127.0.1.1 arch.localdomain arch

# 设置root密码
passwd

# 安装必要软件
pacman -S intel-ucode os-prober grub efibootmgr

# 部署grub
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=grub

# 生成配置文件
grub-mkconfig -o /boot/grub/grub.cfg
```

## 安装桌面

### 重启进入系统

```shell
exit
reboot
```

![](http://image.creat.kim/picgo/20190706082516.png)

![](http://image.creat.kim/picgo/20190706082623.png)

### 安装前准备

```
# 创建交换文件
fallocate -l 4G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile

# 为交换文件设置一个入口
vim /etc/fstab
/swapfile none swap defaults 0 0

# 新建用户
useradd -m -G wheel username
passwd username

# 添加sudo权限
pacman -S sudo
visudo
找到# %wheel ALL=(ALL)ALL 取消注释

reboot
```

### 配置DNS

```
nano /etc/resolv.conf    #编辑配置文件
nameserver 114.114.114.114
nameserver 8.8.8.8
nameserver 8.8.4.4

sudo pacman -S net-tools dnsutils inetutils iproute2
ifconfig
```

![](http://image.creat.kim/picgo/20190706085916.png)



### 安装桌面

```
# 桌面依赖
sudo pacman -S xorg xorg-xinit xterm
# Xfce 桌面
sudo pacman -S xfce4 xfce4-goodies
# KDE(Plasma)桌面
sudo pacman -S plasma kde-applications
# 安装桌面管理器sddm
sudo pacman -S sddm
# 设置开机启动sddm服务
sudo systemctl enable sddm
```

### 配置桌面网络

```shell
sudo systemctl disabtle netctl
sudo systemctl enable NetworkManager
# 显示网络设置图标
sudo pacman -S network-manager-applet
```

## 进入桌面

```shell
reboot
```

![](http://image.creat.kim/picgo/20190706093410.png)

```shell
# 滚动更新
pacman -Syyu
```

## 安装软件

### 安装`vmware-tools`

不要用`VMware`自带的，依赖有问题，用开源的`open-vm-toools`，这里有个开源的安装脚本。https://github.com/rasa/vmware-tools-patches

```shell
su
pacman -S git
git clone https://github.com/rasa/vmware-tools-patches.git
cd vmware-tools-patches
bash patched-open-vm-tools.sh
```

重启`VMware Workstaion` ，此时可以在物理机和虚拟机复制东西了。

### 安装中文输入法

```shell
vim /etc/pacman.conf
# 最后面加上
[archlinuxcn]
Server=https://mirrors.ustc.edu.cn/archlinuxcn/$arch
# 安装yaourt
pacman -Sy yaourt

# 安装archlinuxcn-keyring 包以导入 GPG key
pacman -S archlinuxcn-keyring
# 安装fcitx
pacman -S fcitx fcitx-im fcitx-configtool
# 安装官方字体
pacman -S ttf-roboto noto-fonts noto-fonts-cjk adobe-source-han-sans-cn-fonts adobe-source-han-serif-cn-fonts ttf-dejavu wqy-zenhei ttf-fireflysung ttf-liberation wqy-microhei adobe-source-code-pro-fonts
# 安装谷歌拼音
pacman -S fcitx-googlepinyin
# 添加gtk,qt支持
vim /etc/profile
# 加上
export LANGUAGE=zh_CN:en_US
export XMODIFIERS="@im=fcitx"
export GTK_IM_MODULE="fcitx"
export QT_IM_MODULE="fcitx"

# 切换中文环境
vim /etc/locale.conf
LANG=zh_CN.UTF-8

reboot
```

### 正式修改软件源

```shell
kate /etc/pacman.d/mirrorlist
# 将以下源放在前面，其余所有源注释掉
## China
Server = http://mirrors.163.com/archlinux/$repo/os/$arch
Server = http://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
Server = http://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
Server = http://mirrors.neusoft.edu.cn/archlinux/$repo/os/$arch
Server = http://mirrors.zju.edu.cn/archlinux/$repo/os/$arch
Server = http://mirror.lzu.edu.cn/archlinux/$repo/os/$arch

## Hong Kong
Server = http://mirror.xtom.com.hk/archlinux/$repo/os/$arch
Server = http://mirrors.kurnode.com/archlinux/$repo/os/$arch
Server = http://mirror-hk.koddos.net/archlinux/$repo/os/$arch

## United States
Server = http://mirror.umd.edu/archlinux/$repo/os/$arch
Server = http://archlinux.surlyjake.com/archlinux/$repo/os/$arch
```

### 安装chromium

```shell
pacman -S chromium
```

### 安装pip

```
wget https://bootstrap.pypa.io/get-pip.py
ls -al /usr/bin/python*
python2 get-pip.py
python3 get-pip.py
```

### 安装musicbox

```shell
pacman -S mpg123
git clone https://github.com/darknessomi/musicbox.git && cd musicbox
python(3) setup.py install
```

### 无道词典

```shell
git clone https://github.com/ChestnutHeng/Wudao-dict.git
pip3 install bs4 lxml
sudo bash setup.sh
```

## 美化

### 终端配色

```
# pacman 彩色输出
vim /etc/pacman.conf
# 将color注释取消
```

![](http://image.creat.kim/picgo/20190706155921.png)

## 代理使用

* [LInux下使用V2ray客户端](https://www.creat.kim/archives/64/)

## 结语

写这个教程的目的是为了记录玩`ArchLLinux`遇到的坑，帮助更多的人少踩坑，当然还是为了给以后自己玩`ArchLinux`作为参考资料。
