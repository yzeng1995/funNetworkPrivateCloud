# funNetworkPrivateCloud

Copyright@Yan Zeng, 旋律果子

E-mail: zengyan16@mails.ucas.edu.cn

QQ Group: 1084369522

Telegram Group: https://t.me/melodyNetworkTech

武汉凌讯网络科技有限公司

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

```

## Linux

``` bash
fdisk -l # 显示所有磁盘,根据磁盘的大小判断哪一个是要制作的安装盘
# Linux系统一般不会自动挂载U盘，如果被自动挂载，可以用下面的命令卸载U盘。
umount /dev/sdb1  #后面要卸载的磁盘根据实际情况填写
# 将iso镜像烧录到U盘里,这里注意和MacOS系统的区别，bs这里的M是大写
dd if=./CentOS-8.3.2011-x86_64-dvd1.iso of=/dev/sdb bs=4M

```



## Windows
``` bash
# 随便找一个U盘烧录工具烧录即可。(自己找吧，我帮不了忙，纯净版的太难找了)
# 可以用rufus
# https://rufus.ie

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
