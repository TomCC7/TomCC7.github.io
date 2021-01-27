---
layout: post
title:  "RaspberryPi Configuration"
categories: configurations
---
[toc]

# 系统

## 简介

​	这个教程利用树莓派作为主机并使用nextcloud建立一个私有云盘服务，利用寝室的**公网ip**可以很方便地从外部访问云盘。如果你正好有一块闲置的固态硬盘不妨做一个试试。

### Nextcloud

> ​	[**Nextcloud**](https://nextcloud.com/)是一套用于创建[网络硬盘](https://zh.wikipedia.org/wiki/网络硬盘)的[客户端－服务器软件](https://zh.wikipedia.org/wiki/主從式架構)。其功能与[Dropbox](https://zh.wikipedia.org/wiki/Dropbox)相近，但Nextcloud是[自由及开放源代码软件](https://zh.wikipedia.org/wiki/自由及开放源代码软件)，每个人都可以在私人服务器上安装并运行它。
>
> ​	与Dropbox等专有服务相比，Nextcloud的开放架构让用户可以利用应用程序的方式在服务器上新增额外的功能，并让用户可以完全掌控自己的数据。

​	以上内容摘自维基百科，与网络文件系统相比，nextcloud更像一个百度网盘，你可以从几乎任何平台访问你的文件。

![nextcloud](https://www.player7.cc:44343/uploads/big/bf48e2258b4300af73e48522a98fcbab.png)

## 材料准备

+ 树莓派-作为主机，建议使用4B+（有**千兆网口**）的1GB版本
+ microsd卡-用于存放系统
+ 移动硬盘-用于存放数据
+ 网线-最好使用有线网连接，没有用wifi也可以

## 树莓派启动&配置

### 刷系统

​	这里使用系统raspbian buster的桌面版本。由于官网的镜像下载速度非常慢，我已将镜像上传到[百度网盘](https://pan.baidu.com/s/1lBbbe2MyQIu2Lpu8VcKH5A)(提取码：g3it)。

​	烧写器可以使用[etcher](https://www.balena.io/etcher/)，一款免费的全平台烧写器。下载好镜像和烧写器后就可以开始烧写了，烧写过程会比较慢。

![etcher](https://www.player7.cc:44343/uploads/big/49979035be9e018aad0408e22dc5cde7.png)

​	烧写完成后我们会发现sd卡被分成了两个区，其中boot区会在启动时被读取，而rootfs区存放了系统文件。

![分区](https://www.player7.cc:44343/uploads/big/205c86eacc7053f91975a3ca0454cbd8.png)

### 启动&ssh连接

#### 启动ssh

​	启动ssh非常简单，在boot分区中创建一个名为SSH的空文件即可。提供一种方法：新建一个文本，重命名为SSH（无后缀。

#### 配置wifi

​	**如果插网线启动可以直接跳过这步。**

​	在boot目录下创建一个名为wpa_supplicant.conf的文件，填入以下信息：

```json
country=GB
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
	ssid="wifi名"
	psk="密码"
	priority=正整数，越大优先级越高
}
network={
	ssid="更多wifi名"
	psk="密码"
	priority=正整数，越大优先级越高
}
......
```

​	需要注意的是wifi名和密码都需要使用双引号包起来。配置完wifi后将microsd卡正面朝上插入树莓派，就可以启动了，启动后我们放入的两个文件将被自动读取后删除。

### 连接

​	要进行连接首先要获取你树莓派的ip地址，最简单的方法是登录路由器的管理页面查看。获取ip地址后就可以通过ssh登录树莓派了，这里使用一款ssh工具[putty](https://www.putty.org/)进行连接。

​	![putty](https://www.player7.cc:44343/uploads/big/b5ef01abf47b5adb10cfcc9b4cf9b944.png)

​	在ip地址栏输入树莓派的ip（上图为我的），接下来点*Open*就能够连接了。连接成功后如下图所示：

![puttylogin](https://www.player7.cc:44343/uploads/big/c827899853e70782d325f628cac6d469.png)

​	树莓派的初始用户名为'pi',密码为'raspberry'。输入后看到以下界面就代表登录成功啦。

![puttylogind](https://www.player7.cc:44343/uploads/big/121ee664e09d363ce80479a9b171edd8.png)

### 配置树莓派

#### 连vpn

```bash
export http_proxy=http://192.168.0.109:12333 && export https_proxy=http://192.168.0.109:12333
```



#### 系统设置

​	在命令行中输入`sudo raspi-config`可进入raspbian系统的设置界面：

![raspi-config](https://www.player7.cc:44343/uploads/big/3055fa355b45a15ffb6bd570e8b15a76.png)

​	进入设置界面后，我们从上往下进行设置更改

1. 在`Change User Password`中修改登录密码
2. 在`Localisation Options`中选择`Change Timezone`后选择`Shanghai`
3. 在`Advanced Options`中选择`Expand Filesystem`将系统分区扩展至整个sd卡
4. 最后选择`Finish`重启树莓派并应用设置

#### 换源&更新软件

​	由于接下来会需要下载许多软件，修改软件源会使这一过程更加迅速。

​	这里使用[清华镜像站](https://mirror.tuna.tsinghua.edu.cn/help/raspbian/)提供的raspbian镜像（wsm交大没有）。

​	键入命令`sudo nano /etc/apt/sources.list`，删除原文件所有内容，用以下内容取代：

```
deb http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ buster main non-free contrib
deb-src http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ buster main non-free contrib
```

​	接下来按ctrl+O后按回车保存文件，按ctrl+X退出。

​	同样的，输入`sudo nano /etc/apt/sources.list.d/raspi.list`，删除原文件所有内容，用以下内容取代：

```
deb http://mirrors.tuna.tsinghua.edu.cn/raspberrypi/ buster main ui
```

​	同样保存退出后输入`sudo apt update`更新软件源，换源部分完成。

​	最后，输入`sudo apt upgrade`更新一下软件，这一步不做也可以。

#### PIP换源

```bash
mkdir .pip
vim .pip/pip.conf
#复制以下
[global]
timeout = 10
index-url =  http://mirrors.aliyun.com/pypi/simple/
extra-index-url= http://pypi.douban.com/simple/
[install]
trusted-host=
    mirrors.aliyun.com
    pypi.douban.com
```



#### 设置ssh密钥登录

```bash
# 登录树梅派
mkdir .ssh
vim .ssh/authorized_keys

# 复制公钥 #

# 设置权限
sudo chmod -R 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys 

# 修改sshd设置
sudo vim /etc/ssh/sshd_config
###
RSAAuthentication yes
PubkeyAuthentication yes
PermitRootLogin no
###
# PasswordAuthentication no #禁用密码登录
sudo service sshd restart #重启服务
```



#### 开机自启服务配置-jupyter为例

```bash
sudo vim /etc/systemd/system/jupyter.service
# 复制以下
[Unit]
Description=Jupyter Notebook
After=network.target

[Service]
Type=simple
PIDFile=/run/jupyter.pid 
#这里在/run/目录下没有jupyter.pid,搜索相关信息发现,这个是进程产生之后出现的,虽然在启动前没有,但是可以使用.
ExecStart=/home/pi/.local/bin/jupyter-notebook --config=/home/pi/.jupyter/jupyter_notebook_config.py 
#ExecStart 是执行文件 --config是配置文件,之前我已经配置过jupyter_notebook_config.py,有的教程是配置.json文件(没试过)
User=pi
Group=pi
WorkingDirectory=/home/pi/Desktop/jupyter_project 
#自己设置的工作目录,需要同时在jupyter_notebook_config.py中设置c.NotebookApp.notebook_dir = '/home/pi/Desktop/jupyter_project' 配置自己的工作目录
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target

# 运行以下命令
sudo systemctl enable jupyter.service
sudo service jupyter start
```



# 软件

### TeamViewer

```bash
mkdir Downloads
cd Downloads
wget https://download.teamviewer.com/download/linux/teamviewer-host_armhf.deb
sudo dpkg -i teamviewer-host_armhf.deb
sudo apt install -fy
sudo dpkg -i teamviewer-host_armhf.deb
sudo reboot #重启电脑
teamviewer setup
teamviewer passwd=... #设置密码才能连接
teamviewer info #查看uid
```

> 命令行下无法启动:https://blog.csdn.net/cocoaqin/article/details/100938804

# 树莓派GPIO

## 配置

```bash
sudo raspi-config
#开启 remote-gpio, spi, i2c
# reboot
## 下载wiringpi
wget https://project-downloads.drogon.net/wiringpi-latest.deb #下载
sudo dpkg -i wiringpi-latest.deb #安装
```

## WiringPi

> http://wiringpi.com/reference/