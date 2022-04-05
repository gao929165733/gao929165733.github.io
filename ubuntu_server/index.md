# Ubuntu_server


# Ubuntu server深度学习工作站环境搭建

## 1. 环境配置

- ubuntu server 20.04
- cuda 11.4
- torch 1.9
- miniconda-py3.7

## 2. 具体步骤

### 2.1 系统安装

#### 准备工具

- U盘一个
- [Ubuntu 20.04 LTS 镜像](https://mirrors.tuna.tsinghua.edu.cn/ubuntu-releases/20.04/)
- [启动盘制作工具Rufus](http://rufus.ie/zh/)

#### 安装系统

参考链接如下

- [Ubuntu20.04安装详细图文教程（双系统）](https://blog.csdn.net/hwh295/article/details/113409389)
- [【ubuntu20.04】10分钟win10安装ubuntu20.04双系统（无需Bios设置）](https://www.bilibili.com/video/BV11k4y1k7Li?spm_id_from=333.337.search-card.all.click)
- [Ubuntu 20.04 LTS 基础命令 软件安装 Shell编程](https://www.bilibili.com/video/BV1VK4y1J7pm?spm_id_from=333.337.search-card.all.click)
- [Ubuntu Server 20.04 LTS 安装教程](https://www.bilibili.com/video/BV1KK411s7ik?spm_id_from=333.337.search-card.all.click)

注意

- 服务器的网卡是千兆的，路由器百兆网卡的话，会出现网线口不亮的情况，换路由器上的千兆口就好了
- [相关链接](https://blog.csdn.net/weixin_48544396/article/details/108433205)

### 2.2 配置SSH(远程连接-内网)

- 查看服务器的ssh服务是否开启`sudo ps -e | grep ssh`，如果系统默认没有安装开启，参考步骤：[在Ubantu18.04上开启ssh服务，实现远程连接](https://blog.csdn.net/hwh295/article/details/113409389)

- 下载安装Xshell（远程连接控制端）和Xftp（文件传输）软件，学生是教育邮箱免费使用
- 在服务器查看ip地址：先`sudo apt install net-tools`，然后使用`ifconfig`命令
- 端口号默认为22
- 使用Xshell新建会话连接，输入主机（ip地址）、端口号、用户名和密码；Xftp同理

### 2.3 深度学习环境配置

#### 软件源配置

- 输入以下命令备份原来的源
  - `sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak`
- 再输入以下命令打开sources.list配置文件更换源
  - 阿里云镜像源
    - `deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse deb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse`
  - 清华大学镜像源
    - `deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse # deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse`
- 输入命令更新源
  - `sudo apt-get update`
- 输入命令更新一下软件即可
  - `sudo apt-get upgrade`

#### miniconda安装

- 软件下载，在[这里](https://docs.conda.io/en/latest/miniconda.html)选择你需要的版本下载到指定路径（随意，一般是用户名文件下，或者是/usr/local下）

- 安装

  ![](/pic/5/1.png)

#### Cuda安装

- 办法1：下载安装

  - ![](/pic/5/2.png)
  - ![](/pic/5/3.png)
  - ![](/pic/5/4.png)

- 办法2：自动安装

  - 配置源，更新源，上面已经进行就不需要了
  - 让Ubuntu自己安装显卡驱动（自动搭配安装cuda），以下两句命令都可以
    - `ubuntu-drivers devices`
    - `sudo ubuntu-drivers autoinstall`
  - 等待ubuntu自己安装驱动，而且会把cuda一并装好。装好之后重启电脑，再开机独立显卡就正常工作

  - `nvidia-smi`可以成功显示显卡状态，看到驱动版本和cuda版本则安装成功
  - **这一步会默认安装最新的驱动，自然也是最新的cuda，比如cuda 11.4**。你可能有顾虑：Pytorch只支持到cuda 11.1，paddle只支持到11.2，我用11.4能行吗？经过测试，**cuda大版本号内向下兼容（至少11.4兼容11.2和11.1），可以正常使用**

#### 深度学习框架安装

- 这一步比较简单，网上有很多教程可以找到，网速慢的话，记得先更新conda源，然后再使用conda命令安装
- `conda install pytorch=1.9 torchvision torchaudio cudatoolkit=11.1 -c pytorch`

## 参考链接

- [给小白的ubuntu深度学习服务器装机教程](https://blog.csdn.net/hwh295/article/details/113409389)
- [Ubuntu 20.04 Cuda深度学习环境极！简！安装](https://zhuanlan.zhihu.com/p/397690803?utm_source=wechat_session&utm_medium=social&utm_oi=866248966176641024)
- [【第一篇】Ubuntu 16.04 服务器深度学习环境共用方案（搭建、分配、克隆、远程）](https://zhuanlan.zhihu.com/p/59559936?utm_source=wechat_session&utm_medium=social&utm_oi=866248966176641024)
- [Ubuntu 20.04 LTS 基础命令 软件安装 Shell编程](https://www.bilibili.com/video/BV1VK4y1J7pm?spm_id_from=333.337.search-card.all.click)
- [ubuntu20.04如何更换国内源](https://www.yisu.com/ask/4042.html)
- [在Ubantu18.04上开启ssh服务，实现远程连接](https://blog.csdn.net/weixin_42739326/article/details/82260588)
- [服务器和笔记本直连，网口灯不亮](https://blog.csdn.net/weixin_48544396/article/details/108433205)
- [手把手教你如何在Ubuntu下安装Miniconda](https://os.51cto.com/article/636125.html)
- [清华大学开源软件镜像站-ubuntu20.04下载](https://mirrors.tuna.tsinghua.edu.cn/ubuntu-releases/20.04/)


