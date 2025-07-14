# 1. 为什么使用docker
主要是**openfoam+liggghts**和**ROMS**这两软件依赖比较复杂，将其安装至集群上更是麻烦，不如直接使用docker容器化来得方便。再者则是弄机器人的话也是需要使用docker，方便我换端和上云之类的。

# 2. Docker Desktop安装
Docker由于在比较复杂多容器的情况下，最好有docker compose来进行多容器编排和通信，而最方便的获取dockers和docker compose的方式就是直接下载Docker Desktop了。

ps：docker engine的安装方法又有很多：Docker Desktop（最推荐也是我们采用的方法）、设置并安装Docker Engine存储库、手动安装并手动管理升级、便捷脚本安装。
## 2.1 Ubuntu端 Docker Desktop安装
本节内容主要基于Docker官方手册进行安装
>[Install Docker Desktop on Linux](https://docs.docker.com/desktop/setup/install/linux/)  
>[Install Docker Engine on Ubuntu](https://docs.docker.com/engine/install/ubuntu/)

接下来先进行Docker安装的准备：
1. **KVM**支持。Docker Desktop 运行需要 KVM 支持的 VM。首先需要加载KVM：  
   ```
   modprobe kvm
   modprobe kvm_intel  
   ```
   加载好对应硬件的KVM后可以通过以下命令检查KVM是否已经启动：  
   ```
   lsmod | grep kvm
   ```  
   后续设置的权限似乎我并没有进行，所以暂且先不管。
2. **检查系统**。根据Docker官方的说法，目前docker兼容的Ubuntu系统包括**Ubuntu Oracular 24.10**、**Ubuntu Jammy 22.04 (LTS)**和**Ubuntu Noble 24.04 (LTS)**，恰巧笔者的Ubuntu系统是24.04 LTS，因此继续进行下一步。  
3. **卸载非官方包**。接下来是卸载旧的docker软件包或者非官方包，官方直接提供了卸载命令：  
   ```
   for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
   ```

   但是如果之前的docker里面有储存数据，这样做是不会直接删除的，所以需要注意有其他容器之类的数据还需要手动删除，因为我们是初次安装所以不用顾虑这点。  
4. **GNOME terminal安装**。由于我似乎并不适用GNOME，因此需要先安装Gnome terminal：  
   ```
   sudo apt install gnome-terminal
   ```

下面进行正式的Docker Desktop的安装：  
1. **设置Docker的储存库**。通过以下命令快速设置docker储存库：
   ```
   # Add Docker's official GPG key:
   sudo apt-get update
   sudo apt-get install ca-certificates curl
   sudo install -m 0755 -d /etc/apt/keyrings
   sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
   sudo chmod a+r /etc/apt/keyrings/docker.asc

   # Add the repository to Apt sources:
   echo \
     "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
     $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
     sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   sudo apt-get update
   ```  
   *问题1：*
   ```
   sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
   ```  
   笔者这一步由于网络问题等原因出现以下报错：  
   >curl: (35) Recv failure: 连接被对方重置  
   
   为此进行了换源操作：  
   ```
   sudo curl -fsSL https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
   ```
   *问题2：*
   ```
   echo \
     "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
     $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
     sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```
   笔者在这一步出现了*docker.com*一系列网址都有忽略的情况：
   >忽略:2 https://download.docker.com/linux/ubuntu noble InRelease  
   >错误:2 https://download.docker.com/linux/ubuntu noble InRelease  
   
   但是比较奇怪的是，检查过一系列的链接都是正常的，手动进入网站内也是能打开的，能看到里面的东西，重新单独执行这一步之后又能正常连接了。
2. [**下载Docker Desktop的.deb包**](https://desktop.docker.com/linux/main/amd64/docker-desktop-amd64.deb?utm_source=docker&utm_medium=webreferral&utm_campaign=docs-driven-download-linux-amd64&_gl=1*vjhvsh*_ga*MTEzMjE3NTQ3LjE3NTI0MTAyMzU.*_ga_XJWPQMJYHQ*czE3NTI0OTc4MzkkbzQkZzEkdDE3NTI0OTgzMzAkajU0JGwwJGgw)(需要注意的是，有可能需要科学上网)
3. **安装软件包**，代码如下：
   ```
    sudo apt-get update
    sudo apt-get install ./docker-desktop-amd64.deb
   ```  
   不过安装结束后，还会有一个错误信息，官方表示这个错误可忽略（这是由于_apt文件夹权限不足导致切换为root的提示）：
   >N: Download is performed unsandboxed as root, as file '/home/user/Downloads/docker-desktop.deb' couldn't be accessed by user '_apt'. - pkgAcquire::Run (13: Permission denied)  
   
   最后则是提一嘴，Docker Desktop默认安装在 :
   >/opt/docker-desktop
4. **安装Docker Desktop中文包**，