---
layout:     post
title:      黒域补丁制作
subtitle:   纯手机操作（需root）
date:       2017-07-17
author:    比丘
header-img: img/post-bg-coffee.jpeg
catalog: true
tags:
    - 安卓
    - 黒域
---

#### 准备工作  
1. 安装linux deploy，选择ubuntu16.04，arm64，目录/文件安装，具体请参考[linx deploy 部署-简书](http://www.jianshu.com/p/690e3e35a38f)  ，ssh一定要选，sftp要开启（便于使用，se文件管理器支持sftp）
2. 配置java环境变量，下载java源代码包 [下载地址](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 架构请选择与部署的相同  
###### 配置环境变量 
参考[ubuntu配置java](http://www.cnblogs.com/davidsky/archive/2013/06/01/3113310.html) 
![](http://ot6z44ono.bkt.clouddn.com/17-7-17/80152091.jpg)  
  
  
**profile (建议用se修改，vim太累😂😂😂)**
```
# set jdk environment
export JAVA_HOME=/opt/jdk1.8.0_131
export JRE_HOME=/opt/jdk1.8.0_131/jre
export CLASSPATH=.:$CLASSPATH:$JAVA_HOME/lib:$JRE_HOME/lib
export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
$JRE_HOME/bin
# 刷新配置
source /etc/profile
```
**打补丁过程中要使用sudo或root用户，root用户使用java需配置environment**，如图![](http://ot6z44ono.bkt.clouddn.com/17-7-17/27882679.jpg)
 environment (建议用se修改，vim太累😂😂😂)
 ```
 PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:$JAVA_HOME/bin"
 export CLASSPATH=.:$CLASSPATH:$JAVA_HOME/lib:$JRE_HOME/lib
export JAVA_HOME=/opt/jdk1.8.0_131
 ```
###### 下载 baksmali.jar oat2dex.jar smali.jar 及黒域所需文件： [百度云](http://pan.baidu.com/s/1i566lk5)
###### 提取services.jar/odex化后所需文件及jar文件放于同一文件夹内
 
#### 打补丁
 
 >*root用户运行或者sudo运行  *
 
```
 cd <补丁文件目录>
```  

```
 未odex化
 
 java -jar baksmali-2.2.1.jar d services.jar -o services  
--------------------------------------------------- 
 Android 5.0 - Android 5.1：odex化
 
 java -Xms1g -jar oat2dex.jar boot <boot.oat路径>
 java -Xms1g -jar oat2dex.jar <services.odex路径> <dex目录路径>
 java -Xms1g -jar baksmali-2.2.1.jar d <services.dex路径> -o services
 
 执行完以后，会生成一个新目录services以及中间过程目录odex与dex。
 --------------------------------------------------
 Android 6.0 - Android 7.1：odex化
 
 java -Xms1g -jar baksmali-2.2.1.jar x -d <boot.oat所在目录路径> <services.odex文件路径> -o services

注意: Android 7.0 及以上版本中的boot.oat依赖同目录下的其它文件，请不要移动boot.oat，直接指定它所在的目录就好。
如果boot.oat或services.odex出现在多个目录，使用任何一个就好。注意目录路径与文件路径区别

执行完以后，会生成一个新目录services。
```
```
java -jar baksmali-2.2.1.jar d hy.apk -o apk
python patch.py -a apk -s services
--------------------------------------------------- 
执行完以后，会提示一共打了多个个补丁。正常的话，Android 4.4 一共有 14 处补丁，5.0 以上共有 15 处补丁。
--------------------------------------------------- 
java -jar smali-2.2.1.jar a -o classes.dex services
jar -cvf services.jar classes.dex
```
- **odex**  
![](http://ot6z44ono.bkt.clouddn.com/17-7-17/58677974.jpg)
- **未odex**
![](http://ot6z44ono.bkt.clouddn.com/17-7-17/46715367.jpg)  

#### 替换文件,改权限0644，重启即可

**调用smali以及baksmali时需要处理较长时间，请耐心等待**  
<strike>若出现无法使用python，请source /etc/enviorment</strike>
