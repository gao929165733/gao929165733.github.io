# Ubuntu_server显卡驱动自动更新解决


# Ubuntu_server显卡驱动自动更新解决

## 1. 问题描述

- 系统更新了内核，导致之前配置好的显卡驱动更新，无法使用显卡，具体表现为：
  - nvidia-smi命令报错："NVIDIA-SMI has failed because it couldn't communicate with the NVIDIA driver. 
    Make sure that the latest NVIDIA driver is installed and running."


## 2. 具体步骤

### 2.1 检查当前驱动的版本

- 查看已安装驱动的版本信息：`ls /usr/src | grep nvidia`
  - 无任何返回内容，则说明需要重新安装驱动；直接按照**2.2**往下依次进行，先卸载之前驱动的文件，再安装新的驱动。
  - 返回了驱动的版本信息，直接按照**2.4**往下进行。
- `nvcc -V` 检查显卡驱动信息，是否适配cuda版本

### 2.2 卸载原有的驱动

- `sudo apt-get --purge remove nvidia*`
- `sudo apt autoremove`
- To remove CUDA Toolkit: `sudo apt-get --purge remove "*cublas*" "cuda*"`
- To remove NVIDIA Drivers: `sudo apt-get --purge remove "*nvidia*"`

### 2.3 安装新的驱动

- 更新apt-get：`sudo apt update`
- 查看更新后可以安装的显卡驱动版本，不一定要选择系统推荐的版本：`ubuntu-drivers devices`
- 安装列表给出的显卡驱动版本：`sudo apt-get install nvidia-xxx`

- 禁用**nouveau** （非必要，如果要禁用应该在安装之前禁用）
  - 验证是否禁用 nouveau：`lsmod | grep nouveau`
  - 若有输出，说明没有禁用，进行以下操作禁用：`sudo gedit /etc/modprobe.d/blacklist.conf`
    - 在文件末尾中添加两条：
    - `blacklist nouveau`
    - `options nouveau modeset=0`
  - 更新配置：`sudo update-initramfs -u`
  - 重启电脑
  - 查看是否禁用 nouveau，若无输出，则已禁用：`lsmod | grep nouveau`

### 2.4 重装（关键）

- `sudo apt install dkms`
- `sudo dkms install -m nvidia -v xxx` 注意：**xxx**是指**2.1**部分返回的驱动版本

## 参考链接

- [无法连接NVIDIA驱动：NVIDIA-SMI has failed because it couldn't communicate with the NVIDIA driver](https://blog.csdn.net/hwh295/article/details/113409389)
- [Ubuntu Nvidia-smi has failed 无法通信问题解决](https://zhuanlan.zhihu.com/p/337013545)


