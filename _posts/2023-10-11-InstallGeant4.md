---
layout: post
title: "Geant4安装教程汇总"
subtitle: ""
author: "鹏少"
header-img: "img/geant4.png"
header-mask: 0.2
tags:
  - Geant4
  - Simulation
  - Ubuntu
  - Linux
  - Shell
---

# 教程说明
- geant4在Linux、windows、mac三种系统上都可以安装。本教程主要针对Linux系统，和Windows下的Linux子系统(wsl)，或者虚拟机linux系统。如果是用wsl系统，建议用wsl2，因为wsl2自带可视化解决方案。
- 除了本教程的编译安装方法，还可以通过conda命令或者一些特殊的工具，一键安装geant4， 大家也可以尝试, 具体参考["Geant4官网安装说明"](https://geant4-userdoc.web.cern.ch/UsersGuides/InstallationGuide/html/index.html)。
- 本教程可用于ubuntu18.04/20.04这两个版本。对于ubuntu22.04，没有测试过，不过问题不大。对于debian系其他linux发行版，应该问题也不大。debian系以外的其他linux发行版和macOS， 则不能直接采用。
- 本教程里涉及geant4版本编号命令，记得修改为实际安装的版本编号。此外，不同版本的geant4在安装过程中存在一些差异，本教程提供了一些说明，但没有完全覆盖。
- 本教程里username替换成实际的用户名, /path/to替换成实际的文件夹路径。
- 本教程里所用shell为zsh。对应环境变量的配置文件为~/.zshrc。如果用的是bash，那么对应的配置文件为~/.bashrc
- 编译安装过程中可能有一些warning，不报错就没事。
- 本教程的内容汇总了网上许多优秀的教程，由于之前记录的不仔细，无法一一附上，十分抱歉。


# 第一步-准备工作-安装必要的包 
这一步安装过后，如果编译安装出问题，最准确的debug的参考为：官网上的安装要求[Geant4 System/Software Prerequisites — Geant4 Installation Guide 11.1 documentation](https://geant4-userdoc.web.cern.ch/UsersGuides/InstallationGuide/html/gettingstarted.html)

对于依赖包列表，笔者手里有多个版本，跟geant4版本和linux版本有关，一并放上。如果想省事，可以都运行安装（有些包可能会安装不上，可以先不管，后面编译如果出问题再看）

版本1：
```bash
sudo apt install git  dpkg-dev  cmake  g++  gcc  libdpkg-dev binutils  libx11-dev  libxpm-dev  libxft-dev  libxext-dev  libxmu-dev  libpng-dev libjpeg-dev gfortran  libssl-dev  libpcre3-dev  libglew-dev  libftgl-dev  libmysqlclient-dev  libfftw3-dev  libcfitsio-dev  graphviz-dev  libavahi-compat-libdnssd-dev libldap2-dev  python-dev  libxml2-dev  libkrb5-dev libgsl-dev 
```

版本2: 这里面安装的是qt5， 适用于ubuntu20及以上版本
```bash
sudo apt install libxerces-c-dev libmotif-dev libglw1-mesa-dev inventor-dev opticalraytracer libpythia8-dev pythia8-doc-html pythia8-doc-worksheet pythia8-examples emacs colordiff lftp dkms build-essential libzlcore-dev libxmu-dev fastjet-doc fastjet-examples libfastjet-fortran-dev libfastjetplugins-dev libfastjettools-dev libfastjet-fortran0 libfastjetplugins0 libfastjettools0 vim vim-doc vim-scripts  qt5-default qtcreator 
```

版本3: 这里面安装的是qt4， 适用于ubuntu20以下版本
```bash
sudo apt install cmake git dpkg-dev libdpkg-dev libx11-dev libxpm-dev libxft-dev libxext-dev libjpeg-dev python-dev gfortran libssl-dev libpcre3-dev libglu1-mesa-dev libglew-dev libftgl-dev libmysqlclient-dev libfftw3-dev libcfitsio-dev:i386 libcfitsio-dev libgraphviz-dev libavahi-compat-libdnssd-dev libdap-dev libxml2-dev libgsl0-dev libqt4-dev libpnglite-dev libafterimage-dev libgmp-dev libpq-dev libsqlite3-dev libiodbc2-dev libdpm-dev libavahi-core-dev
```

版本4：
```bash
sudo apt-get install dpkg-dev g++ gcc binutils libx11-dev libxpm-dev libxft-dev libxext-dev python libssl-dev gfortran libpcre3-dev xlibmesa-glu-dev libglew1.5-dev libftgl-dev libmysqlclient-dev libfftw3-dev libcfitsio-dev graphviz-dev libavahi-compat-libdnssd-dev libldap2-dev python-dev libxml2-dev libkrb5-dev libgsl0-dev libxerces-c-dev
```

# 第二步-源码和数据下载
Geant4的[官方下载页面](https://geant4.web.cern.ch/support/download)上把**Source files**(GNU or Linux tar format, compressed using gzip)和**Data files**下载下来，可能需要科学上网；或者直接用命令下载
```bash
wget https://gitlab.cern.ch/geant4/geant4/-/archive/v11.1.2/geant4-v11.1.2.tar.gz
```

下载下来以后，所有的Data files放到一个目录（`path/to/geant4-data`）里头去，然后在这个目录下执行以下命令一键解压：
```bash
for i in `ls *.gz` ; do tar -xvf $i ; done
```

# 第三步-编译安装
编译安装包含两步: cmake -> make -> make install

第一步是cmake工具生成makefile，makefile可以理解为大型代码库的编译命令（把庞大源代码库进行联合编译）--速度较快

第二部就是用上述makefile进行编译--速度很慢

第三步是把编译好的文件复制到指定的安装目录--速度较快

这里面一般来说，只要cmake输出正常，没在报错，后续就没有问题。

##### 目录的设计
涉及到四个文件夹,四个文件夹最好是平行关系，不要有嵌套:
- `path/to/geant4-source`：源码目录，下的源码包解压开来以后的文件夹，包括CMakeLists.txt等文件，文件夹名字大概是类似于geant4.10.07.p02；
- `path/to/geant4-data`：Data files目录，就上一步把所有Data Files解压出来的那个目录；
- `path/to/geant4-build`：编译目录， 这是个临时目录，安装过后，这个就可以删除；
- `path/to/geant4-install`：最终安装位置

例如用下命令快速建立文件夹, 然后把geant4.10.07.p02.tar.gz解压到geant4.10.07.p02-source里面；把数据包解压到geant4.10.07.p02-data: 

```bash
mkdir geant4 &&cd geant4 &&mkdir geant4.10.07.p02-data&&mkdir geant4.10.07.p02-source&&mkdir geant4.10.07.p02-install&&mkdir geant4.10.07.p02-build
```

##### cmake
不同版本的cmake命令有一些差异, 都放在这里，大家参考

geant4.11.1版本： 

```bash
cd /home/username/Documents/geant4.11.1/geant4.11.1-build
cmake -DCMAKE_INSTALL_PREFIX=/home/username/Documents/geant4.11.1/geant4-v11.1.0-install -DGEANT4_INSTALL_DATADIR=/home/username/Documents/geant4.11.1/geant4-v11.1.0-data -DGEANT4_USE_GDML=ON -DGEANT4_USE_QT=ON -DGEANT4_USE_XM=ON -DGEANT4_USE_OPENGL_X11=ON -DGEANT4_USE_RAYTRACER_X11=ON -DGEANT4_USE_SYSTEM_ZLIB=ON -DGEANT4_USE_INVENTOR=OFF -DGEANT4_BUILD_CXXSTD=17 -DGEANT4_BUILD_MULTITHREADED=ON /home/username/Documents/geant4.11.1/geant4-v11.1.0-source
```

geant4.10.7版本： 
```bash
cmake -DCMAKE_INSTALL_PREFIX=/home/username/Documents/geant4.10.7/geant4.10.07.p02-install -DGEANT4_INSTALL_DATADIR=/home/username/Documents/geant4.10.7/geant4.10.07.p02-data -DGEANT4_USE_GDML=ON -DGEANT4_USE_QT=ON -DGEANT4_USE_XM=ON -DGEANT4_USE_OPENGL_X11=ON -DGEANT4_USE_RAYTRACER_X11=ON -DGEANT4_USE_SYSTEM_ZLIB=ON -DGEANT4_USE_INVENTOR=OFF -DGEANT4_BUILD_CXXSTD=17 /home/username/Documents/geant4.10.7/geant4.10.07.p02-source
```

geant4.10.4版本： 
```bash
cmake -DCMAKE_INSTALL_PREFIX=/home/username/Documents/geant4.10.4/geant4-v10.4.2-install -DGEANT4_INSTALL_DATADIR=/home/username/Documents/geant4.10.4/geant4-v10.4.2-data -DGEANT4_USE_GDML=ON -DGEANT4_USE_QT=ON -DGEANT4_USE_XM=ON -DGEANT4_USE_OPENGL_X11=ON -DGEANT4_USE_RAYTRACER_X11=ON -DGEANT4_USE_SYSTEM_ZLIB=ON -DGEANT4_USE_INVENTOR=OFF -DGEANT4_BUILD_CXXSTD=14 /home/username/Documents/geant4.10.4/geant4-v10.4.2-source
```

###### cmake命令解释
注意：geant4.10.7及以上支持c++17 ，安装的时候也是c++17 geant4.10.4支持c++14

命令解释：
- `DCMAKE_INSTALL_PREFIX`：安装目录；
- `DGEANT4_INSTALL_DATADIR`：Data files的目录
- `DGEANT4_USE_GDML`、`DGEANT4_USE_QT`、`DGEANT4_USE_XM`、`DGEANT4_USE_OPENGL_X11`等等, 启用GDML、QT、XM、OPENGL_X11、RAYTRACER_X11、ZLIB；
    
##### make和install
```bash
make -jN
# 这里N代表要用多少线程，比如最多24个线程，就可以写20
```

上一步执行完以后,执行
```bash
make install
```

# 第四步-环境配置
在.zshrc文件添加: 
```bash
vim ~/.zshrc 
cd /home/username/Documents/geant4/geant4.10.07.p02-install/bin && source geant4.sh &&cd -
cd /home/username/Documents/geant4/geant4.10.07.p02-install/share/Geant4-10.7.2/geant4make && source geant4make.sh && cd -
```

# 第四步-测试
在`path/to/g4-install/share/Geant4-10.7.2`下有一个`examples`文件夹，里头有一些自带的实例，可以测试一下G4能不能跑起来。
```bash
cd path/to/geant4-install/share/Geant4-10.7.2/examples/basic/
cp -r B1/ test/
cd test/
mkdir build
cd build
cmake ../
make -jN
```

make出来大概这样：
```txt
Scanning dependencies of target exampleB1
[ 12%] Building CXX object CMakeFiles/exampleB1.dir/exampleB1.cc.o
[ 25%] Building CXX object CMakeFiles/exampleB1.dir/src/B1ActionInitialization.cc.o
[ 37%] Building CXX object CMakeFiles/exampleB1.dir/src/B1DetectorConstruction.cc.o
[ 50%] Building CXX object CMakeFiles/exampleB1.dir/src/B1EventAction.cc.o
[ 62%] Building CXX object CMakeFiles/exampleB1.dir/src/B1PrimaryGeneratorAction.cc.o
[ 75%] Building CXX object CMakeFiles/exampleB1.dir/src/B1RunAction.cc.o
[ 87%] Building CXX object CMakeFiles/exampleB1.dir/src/B1SteppingAction.cc.o
[100%] Linking CXX executable exampleB1
[100%] Built target exampleB1
```

最后运行编译好的文件：
```bash
./exampleB1
```
看到下图就说明安装已经顺利完成啦：
![](/img/inPosts/image-20200720214700103.png)
```