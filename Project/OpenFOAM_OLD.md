# OpenFOAM集群安装
本文主要记录我在**2025年2月至3月**期间在学院集群安装OpenFOAM的过程。  

## 1.环境配置

### 1.1 mpi配置
**mpi（message passing interface）** 是用于并行计算的协议。学院中的集群已预安装了mpi，分别为intelmpi（v2021.3.0）与openmpi（gnu v4.0.3 、Intel v4.0.3&v4.1.6）。

由于OpenFoam适配的mpi为openmpi，使用非openmpi进行编译会报错：
```
gcc: error: unrecognized command line option ‘--showme:link’
```
因而本例采用openmpi gnu v4.0.3，以下为在集群加载openmpi的步骤：
```
# 在命令行先返回目录；
cd ~
# 可使用module命令查看可加载应用（module avail）、查看已安装应用（module list）、卸载已安装应用（module purge）；
# 记录下需要安装的mpi地址后，使用编辑bashrc，可使用vim、nano等编辑器，本例中使用vim；
vim .bashrc
# 按下i进行编辑模式，按下esc退出编辑模式，输入:w保存，输入:q退出编辑器；
# 在编辑器中先卸载应用，后加载需要的应用；
module purge
module load mpi/openmpi/gnu/4.0.3
# 退出编辑器后加载bashrc文件启用mpi；
source .bashrc
```  

### 1.2 compiler配置
compiler，即编译器用于将openfoam源码进行编译。学院集群预安装了gcc、intel、cmake等编译器，由于低版本gcc编译器具有的glibcxx版本较低，在blockMesh生成网格时会报错：
```
'GLIBCXX_3.4.32' not found
```
因而本例采用gcc v13.2.0，以下是具体调用步骤：
```
# 返回目录，编辑bashrc；
cd ~
vim .bashrc
# 添加加载gcc编译器的命令；
module load compiler/gcc/13.2.0
# 退出编辑器后加载bashrc文件启用compiler；
source .bashrc
```
以上两步便是安装OpenFOAM前的集群环境配置步骤，接下来便是OpenFOAM的下载和编译。  

## 2. OpenFOAM配置
因为考虑到后续可能需要耦合其他插件或软件（如waves2foam，不过这个太久没人维护比较久后面还是没有安装），我选择了com版本的OpenFOAM，**OpenFOAM v2206**，以下是我的安装过程：  
* _ps：OpenFOAM因为历史原因分成了[org](https://openfoam.org/)和[com](https://www.openfoam.com/)两个版本，大致区分为。org版本格式是'OpenFOAM+版本号'，如OpenFOAM 12；com版本格式是'OpenFOAM+v年份月份'，如OpenFOAM v2206。_

### 2.1 OpenFOAM下载
由于集群处于不联网的状态，只能手动下载源码上传到集群后进行编译。  
在OpenFOAM的[release-history页面](https://www.openfoam.com/download/release-history)可以找到OpenFOAM的com版本的历史源码包，在其中下载[OpenFOAM-v2206](https://sourceforge.net/projects/openfoam/files/v2206/OpenFOAM-v2206.tgz/download)和[ThridParty-v2206](https://sourceforge.net/projects/openfoam/files/v2206/ThirdParty-v2206.tgz/download)的源码包。  
下载完成之后，我们得到