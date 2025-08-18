# OpenFOAM耦合LIGGGHTS
CFDEM是开源的耦合了计算流体力学方法（CFD）和离散元法（DEM）的项目，主要通过开发的CFDEM软件耦合OpenFOAM和LIGGGHTS，一个开源离散元法软件。

## 1.CFDEM注册
### 1.1CFDEM相关软件下载
CFDEM®coupling和LIGGGHTS®都在[CFDEM project官网](https://www.cfdem.com/)有较多文档，建议注册使用。  
**！！注意：注册认证邮件容易被邮箱当作垃圾邮件，请务必检查邮箱里的垃圾箱**  
在[官网的CFDEM coupling Version History网页](https://www.cfdem.com/node/414)中，可以看见CFDEM的版本，目前（2025.8.17）最新版本的是 _CFDEM v3.8.1_ ，兼容Ubuntu24.04LTS系统、OpenFOAM-6和LIGGGHTS-PUBLIC 3.8.0；因为我Ubuntu系统就是24.04，因而我编译的便是这个版本的OpenFOAM+CFDEM+LIGGGHTS。  
