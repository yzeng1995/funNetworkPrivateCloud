# funNetworkPrivateCloud

Copyright@Yan Zeng, 旋律果子

E-mail: zengyan16@mails.ucas.edu.cn

QQ Group(付费群): https://www.lingxun.tech/join.html

Telegram Group(免费): https://t.me/melodyNetworkTech

武汉凌讯网络科技有限公司: https://www.lingxun.tech

Bilibili(旋律果子): https://space.bilibili.com/626863

Youtube(旋律果子)：https://www.youtube.com/channel/UC7yQPFk1rCiaZDmXXqLwMhw


# 【好玩的网络-私有云篇】第01期
# 【好玩的网络-私有云篇】第02期
具体的教程请看视频教程，本文档仅仅只做一些补充以及一些命令的讲解。

Centos的官方网站为：https://www.centos.org
## MacOS
``` bash
diskutil list # 显示所有磁盘
diskutil unmount /Volumes/NO\ NAME # 将自动挂载测磁盘先卸载掉
# 将iso镜像烧录到U盘里
dd if=/Users/YanZENG/nfs/disk-d2550/CentOS-8.3.2011-x86_64-dvd1.iso of=/dev/disk3 bs=4m

# 小技巧，在MacOS中，在输出的磁盘前面加r可以提升读写速度，例如：
dd if=/Users/YanZENG/nfs/disk-d2550/CentOS-8.3.2011-x86_64-dvd1.iso of=/dev/rdisk3 bs=4m
# 可以结合pv命令来显示刷写的进度，这个自己去查一下。在刷写的过程中，也可以打开系统
# 自带的监视器来显示拷贝的速度。

```

## Linux

``` bash
fdisk -l # 显示所有磁盘,根据磁盘的大小判断哪一个是要制作的安装盘
# Linux系统一般不会自动挂载U盘，如果被自动挂载，可以用下面的命令卸载U盘。
umount /dev/sdb1  #后面要卸载的磁盘根据实际情况填写
# 将iso镜像烧录到U盘里,这里注意和MacOS系统的区别，bs这里的M是大写
dd if=./CentOS-8.3.2011-x86_64-dvd1.iso of=/dev/sdb bs=4M

# 小技巧，在linux中，加入status的参数可以显示写盘进度，用法如下：
dd if=./CentOS-8.3.2011-x86_64-dvd1.iso of=/dev/sdb bs=4M status=progress
```



## Windows
``` bash
# 随便找一个U盘烧录工具烧录即可。(自己找吧，我帮不了忙，纯净版的太难找了)
# @Potter 提供思路，可以用rufus
# https://rufus.ie

# @本直播间黑粉头子 提供思路，可以使用ventoy
# https://www.ventoy.net/cn/index.html

# redhat 官方推荐 Fedora Media Writer
```


## 安装系统之后，需要用到的命令
``` bash
nmcli # 查看网络状态
nmcli con show # 查看网络连接（主要查看连接名称）
nmcli con up enp1s0 # 启动网络连接（后面接连接名称）
nmcli # 查看网络状态
ping baidu.com # 测试到百度是否连通
nmcli con modify enp1s0 autoconnect yes # 设置某个连接自动连接（插上网线）
exit # 退出终端
# 启动web图形界面
systemctl enable --now cockpit.socket
# (启动完成后浏览器访问 IP地址:9090)
yum -y update  # 更新系统(硬盘直接插到d2550上，首次启动之后更新和设置网络)
reboot # 重启系统

```


## 远程登录
``` bash
ssh root@192.168.4.106
```

基于CentOS 8的适用于64位树莓派的服务器镜像:https://raspberrypi.club/366.html

## 设置SSH开机启动
``` bash
systemctl enable sshd
```
# 【好玩的网络-私有云篇】第03期

## 设置防火墙

``` bash
# 设置防火墙主要是为了方便以后的演示。防火墙默认的zone是public，只开放了22号端口等少量端口。
# 设置zone为trusted，则开放所有的端口，局域网内的设备都能进行访问。
# 安全警告：小白在我没讲网络安全之前，请不要将设备直接暴露在公网中！
firewall-cmd --set-default-zone=trusted # 设置默认区域为trusted区域，开放所有的端口。

```

## 设置selinux

``` bash
# selinux是centos8上的一个安全软件，默认是打开的。为了避免以后的麻烦，现在将它关闭。
setenforce 0 # 临时关闭selinux
# 永久关闭selinux
sed -i "s#SELINUX=enforcing#SELINUX=disabled#g" /etc/selinux/config

```

## 设置分享目录以及安装软件

``` bash
cd /media  # change dictionary, 改变路径，也就是就是进入这个文件夹
ll # 显示当前目录中的所有文件以及文件夹
mkdir public # 在当前目录下创建public目录，相当于建立了public的文件夹

yum -y install nfs-utils # 安装nfs协议软件

```

## 配置分享目录以及启动软件

``` bash
# 将/media/public 目录通过nfs协议分享出去
echo "/media/public 192.168.0.0/16(rw,root_squash,async,insecure) 172.16.0.0/12(rw,root_squash,async,insecure) 10.0.0.0/8(rw,root_squash,async,insecure)" >> /etc/exports

systemctl start nfs-server.service # 启动NFS服务
systemctl enable nfs-server.service # 设置NFS服务开机自行启动

showmount -e localhost # 在本机查看已经成功分享的目录
showmount -e 192.168.4.20 # 在其他机器上查看服务器192.168.4.20上的NFS分享

# 挂载NFS到本地(MacOS和Linux用户)
mount -t nfs 192.168.4.20:/media/public ./nfs-fun # 这条命令在客户机运行，不是在服务器中运行。

# win挂载的方法是映射磁盘驱动器，具体方法自己查一下或者等大佬来补充吧，win对nfs的UTF-8编码支持不是很好)


```

# 挂载新磁盘以及格式化并重新分享

``` bash
fdisk -l # 显示所有磁盘
fdisk /dev/sda  # 给硬盘分区
mkfs.xfs /dev/sda1 # 将分区格式化为xfs格式
cd /media
mkdir disk # 创建新文件夹
mount /dev/sda1 /media/disk  # 将磁盘分区挂载到disk目录下
vim /etc/exports # 修改NFS配置文件
systemctl restart nfs-server.service # 重新启动NFS服务，主要是为了更新配置文件
showmount -e localhost # 查看是否分享成功
chmod 777 -R /media/disk # 修改权限，任何人都可以读，写硬盘

```













