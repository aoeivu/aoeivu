---
layout: mypost
title: OpenLinux Compile
categories: [OpenLinux]
---

# 需要配置的环境
1. 安装ncurses
```
sudo apt-get install libncurses5-dev
```
2. 安装Python2.7
```
sudo apt-get install python
```
3. 安装gcc 4.8
```
sudo apt-get install gcc-4.8
```

# 准备过程
1. 解压SDK包
```
tar -jvxf sdk.tar.bzx
```
2. 设置
```
/ql-ol-sdk/ql-ol-rootfs/etc/data/mobileap_cfg.xml文件中的0改为1
<MobileAPEnableAtBootup>1<MobileAPEnableAtBootup>
```
# 编译过程
1. 用source命令初始化编译环境
```
source /ql-ol-sdk/ql-ol-crosstool/ql-ol-crosstool-env-init
```
2. 在SDK, /ql-ol-sdk路径下编译app文件
```
make extsdk
```
编译完成后将完成的可执行文件，拷贝到/ql-ol-sdk/ql-ol-rootfs/usr/bin/下面
