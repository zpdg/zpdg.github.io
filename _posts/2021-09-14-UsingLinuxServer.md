---
layout: post
title: "Linux服务器使用指南"
subtitle: "工欲善其事必先利其器"
author: "鹏少"
header-img: "img/linux.png"
header-mask: 0.2
tags:
  - Linux
  - Server
  - Ubuntu
---

许多刚接触科研的同学可能会有使用服务器进行计算的需求，而服务器大部分都是基于Linux的，命令行的界面对于初学者比较劝退，学习曲线较为陡峭。本文正是针对Linux服务器的使用指南，不求深，但求实用。

本文主要内容：服务器登陆、文件传输、服务器使用（常用命令、实用工具推荐）、vscode/anaconda远程。

# 引言

首先常见的Linux服务器的系统主要包括debian系（debian、ubuntu）、redhat系(redhat、centos)这两类发行版，同一类发行版用法相似。对于初学者，我们只需要知道这两类系统的区别在于debian系用apt(包管理器)安装软件，redhat系用yum安装软件，除此之外，差距很小。除了软件安装方式的区别，其余Linux命令在不同发行版是通用的。具体判断方法是通过服务器系统根目录下的发行版信息文件，运行如下命令可以判断：

```bash
#如果以下命令正确输出，则说明是debian系，否则为redhat系
cat /etc/lsb-release    
```

> [Linux 发行版](https://linux.cn/article-12609-1.html)是一个由 Linux 内核、GNU 工具、附加软件和软件包管理器组成的操作系统，它也可能包括显示服务器和桌面环境，以用作常规的桌面操作系统。

不过一般用户没有权限安装系统软件（即不能用包管理器安装，也不能安装到系统文件夹，只有管理员有权限），只能把软件安装在自己的账户下。要么通过编译安装，要么直接用可执行文件。

此外，大多数实验室的服务器通常都是集群服务器，包括许多台主机（即服务器节点）以及共享磁盘阵列，配有作业提交系统。一般会有一个管理节点，用于文件的管理，和简单的计算。此外还会有若干计算节点，需要从管理节点登陆计算节点。

# 服务器登陆

要使用服务器，首先我们需要先进入管理节点，这里可能有两种方式，一种是直接连接到Linux服务器，另一种是先连接到一个跳板机（安全起见），然后再连接到Linux服务器。由于PC和跳板机的系统可能不同，这里简单介绍五种场景的远程连接，大家按需取用:

1. Win->Win

  使用场景为Win的PC连接到Win的跳板。直接通过Win自带的远程桌面连接的功能即可（**要求系统在家庭版以上**），在搜索栏可搜到。输入IP地址，点击连接，然后输入管理员分配的用户名和密码即可。

2. Win->Linux

  使用场景为Win的PC连接到Linux服务器，或Win的跳板连接到Linux服务器。如果Win系统比较新（Win10），直接用cmd/power shell/windows shell就可以直接输入**ssh IP**进行连接。否则需要下载Putty或者Xshell等软件，Putty可在[官网](https://www.putty.org/)免费下载，Xshell可在[官网](https://www.netsarang.com/en/free-for-home-school/)申请免费教育版。

  > 官网链接附在文末

3. Linux->Win

	使用场景为Linux的PC连接到Win的跳板。推荐使用远程软件——remmina，Linux发行版一般自带，若需安装直接用包管理器安装即可。具体使用方法：设置协议为RDP，然后输入IP即可进行连接，还可以在设置里调节分辨率和共享文件夹。

4. Mac->Win

	使用场景为Mac连接到Win的跳板。可以使用Microsoft Remote Desktop。 

5. Linux/Mac->Linux

	使用场景为Linux的PC或Mac，连接到Linux服务器或者Linux的跳板。这个直接打开终端，ssh IP即可。

登陆进来后，我们就进入了管理节点，当前路径为自己账户。倘若需要从管理节点进入计算节点，直接输入命令ssh xxx（xxx是节点名称）。

# 服务器文件传输

对于PC，一般是Win10、Linux、Mac系统，其命令行都自带一个工具**scp**，可以很方便的和Linux服务器直接进行文件传输，

**scp**使用命令如下:

```bash
scp -r PCpath  XXX@xxx.xxx.xxx.xxx:Linuxpath
# 这里PCpath是你要传输到服务器的文件在你电脑上的路径，后面是服务器用户名、IP和目标路径
```

但如果服务器是通过Win系统的跳板登陆，则较为麻烦，需要先把文件拷贝到跳板。如果PC是Win10还好，可以直接复制粘贴。如果PC是Linux和Mac，则需要**开启文件夹共享**。[Linux](https://zhuanlan.zhihu.com/p/39522612)和[Mac](https://support.apple.com/zh-cn/guide/mac-help/mh17131/mac)的文件夹共享设置的网页链接已附文末。

文件拷贝到跳板后，然后使用跳板上的软件再传输到Linux服务器上，比如Xftp（教育免费）。当然如果跳板机是版本较新的Win系统也可以用**scp**命令传输到Linux服务器。

此外，需要通过Win系统的跳板登陆的情况，也可以通过配置ssh的配置文件实现。参考[网页链接](https://zhuanlan.zhihu.com/p/74193910)已附文末。

# 服务器使用

登陆进服务器，我们就得到了一个命令行界面，怎么使用呢，这里介绍一些简单的命令，和一些实用工具。

### Linux基础命令

Linux系统里，一切皆文件。文件操作命令很基础，例如：

```bash
# 当前路径（目前在文件系统下哪个位置（哪个文件夹））
pwd
# 列出当前文件夹下的文件和子文件夹
ls 
# 创建一个名为Dir的文件夹
mkdir Dir  
# 查看一个文档的内容，打印到界面
cat FileName  
# 用vim编辑器打开或创建一个文档
vim FileName  
# 将文件F1复制一份，并命名为F2
cp F1 F2 
# 删除文件F
rm F  
# 把F1移动到path路径下，并命名为F2
mv F1 path/F2  
```

以及其他一些常用命令：

```bash
# 解压zip文件
unzip xxx 
# 从网页链接xxx下载文件
wget xxx 
# 测试与xxx主机间网络连通性
ping xxx 

# 若有管理员权限。debian系，用包管理器apt安装管理软件。redhat系替换apt为yum
#安装xxx软件，apt-get也可
sudo apt install  xxx  
#卸载软件
sudo apt remove xxx 
# 更新软件仓库
sudo apt update  
# 更新软件及系统
sudo apt upgrade 
```

更多命令参考[网页链接](https://www.linuxcool.com/)附文末

### 		Linux服务器实用工具

这里分别介绍下几个实用工具，包括vim——文档编辑，tmux——终端复用器（在断开远程时保持程序继续运行），top——后台查询。

#### vim

服务器端会自带且一般只有这一个文档编辑软件。我们需要了解一些基础使用，以应对简单的文档编辑工作。**vim**不同于普通的编辑器，它具有多个模式，通过键盘而不是鼠标来完成高效的文档编辑工作。主要是**insert**、**normal**、**command**三个模式。我们一进去**vim**是**normal**模式，这个模式下无法进行直接输入。要想打字输入，需要按下**insert**键或者**i键**（小写）才能在光标处输入字符或删除字符。要想退出**vim**，则需要按**ESC键**退出**insert**模式，来到**normal**模式，然后输入**:wq**（w代表保存，q代表退出）。

除了**insert**模式的基本使用，结合**normal**和**command**模式可以提高编辑的效率。下面介绍一些常用的命令。注意，输入命令时需要切换到英文输入。

**normal**模式(在**insert**模式按**ESC键**即可进):

```bash
# 剪切当前字符，大写为复制前一个
x
# 剪切当前行
dd  
# 复制当前行
yy 
# 复制剪切板内容到当前字符或当前行后，大写为复制在前面
p   

# 剪切当前字符开始往后的10个字符  
10x  
# 剪切当前行开始往下的10行
10dd 
# 复制当前行开始往下的10行
10yy 
# 往下翻半页
ctrl+d  
# 往上翻半页
ctrl+u  
# 撤销
u  
# 取消撤销
ctrl+r 
```

**command**模式(类似于normal模式，但命令前有前缀，比如":"、"/"等，**且输入后敲回车执行**):

```bash
# 保存退出
:wq 
# 将全文中字符串A替换为字符串B，并逐个请求确认,去掉c则不提供确认（若字符串有特殊字符需要用正则表达式表达）
:%s/A/B/gc  
# 寻找字符串xxx ，然后键入n跳转都下一个，N跳转到上一个
/xxx  
```

更多命令参考[网页链接](https://www.runoob.com/linux/linux-vim.html)附文末

#### tmux 

服务器跑程序时，一时半会跑不完，而我们又不能一直开着远程，这时就需要用到tmux——终端复用器。

具体安装方法如下：

```bash
# 有权限且联网，如果是redhat系,apt改为yum
sudo apt install tmux 

# 否则需要下载,推荐下载appImage，开箱即用
# 倘若服务器联网直接在命令行下载即可，否则需要在PC上下载，然后传输到服务器。
wget https://github.com/nelsonenzo/tmux-appimage/releases/download/tmux3.1b/tmux-3.1b-x86_64.AppImage
# 然后在.bashrc文件最后加上一行(vim ~/.bashrc)
alias tmux="path/tmux-3.1b-x86_64.AppImage"#path是你放置AppImage的位置
```

比如你需要运行一个python程序，然后退出远程时保持程序的运行，使用方法如下:

```bash
# 新建一个tmux会话
tmux   
# 在这个会话的后台运行这个程序，这里不加&，则需要使用快捷键来退出会话
python xxx.py &  
# 退出这个会话，使这个会话在服务器后台运行
tmux detach    
# 退出远程
exit   

## 重新进入远程
# 进入上一个会话查看运行结果
tmux   
# 运行成功，关闭当前tmux会话
exit   
# 退出远程
exit   
```

> 这里一个tmux会话相当于一个可以使之在后台运行的终端


如果想要跑多个程序则需要了解更多命令，如下：

```bash
# 列出当前的tmux会话
tmux ls 
# 进入新会话，会话会自动编号
tmux new 
# 进入新会话，命名为xx
tmux new -s xx 
# xx是会话的编号或名字
tmux a -t xx 

## 快捷键-都是先同时按住ctrl和b键，然后松开再按另一个键
# 退出当前tmux会话  
ctrl+b+d  
#在当前会话打开新的一个窗口，窗口会自动编号
ctrl+b+c  
#切换到当前会话的窗口1
ctrl+b+1  
```

> 对于**exit**命令，表示关闭当前窗口（若一个会话有多个窗口）或会话，或者关闭当前终端（即退出远程）

我们可以通过开多个会话来运行多个程序，也可以一个会话开多个窗口。此外，tmux更多命令和快捷键，如分屏等，参考[博客链接](https://www.cnblogs.com/lizhang4/p/7325086.html)已附文末。

#### top

服务器自带。用来查看服务器的后台、负荷的命令行工具，Linux系统自带。直接在命令行键入**top**，就会出现各种信息，按**q键**退出。

# vscode/anaconda远程

这里介绍下两种常用的远程程序运行的方案。

#### vscode

很多人都在使用vscode，这里介绍下vscode的远程功能。先安装插件Remote-SSH，然后界面左边会出现一个远程管理的图标，如下图，点击加号新建一个远程连接，按照提示输入Linux服务器的：username@IP，接着输入密码即可连接。连接后，vscode会自动在服务器上安装所需要的代码运行插件。

<img src="/img/inPosts/Screenshot%20from%202021-06-15%2011-49-15.png" alt="Screenshot from 2021-06-15 11-49-15"  />

这样我们便可以在PC上用vscode写代码，然后在服务器上运行了。

但如果需要通过跳板登陆则相对麻烦，参考[网页链接](https://zhuanlan.zhihu.com/p/103578899)附文末。

#### anaconda

此外，我们也可以通过在服务器上安装anaconda，并运行jupyter notebook，然后在自己PC的浏览器上输入链接和口令来远程编辑运行程序。安装anaconda及源的配置参考往期推送[Ubuntu配置指南](https://zpatrick99.gitee.io/2020/12/29/ubuntu%E9%85%8D%E7%BD%AE%E6%8C%87%E5%8D%97/)的anaconda部分。

不过，小编最喜欢的一种方式是结合vscode和anaconda的优势，anaconda提供环境，vscode提供远程和文件编辑功能，用vscode的notebook插件，在vscode里运行notebook。





# 写在最后

小编也是最近才熟悉了Linux服务器的使用，于是现学现卖总结成了这篇推送，希望能够帮到大家。BTW，推荐一个更好的学习Linux的方法——直接在PC上装一个Linux桌面版（比如Ubuntu双系统），强迫自己熟悉Linux的使用，系统配置可以参考[Ubuntu配置指南](https://zpatrick99.gitee.io/2020/12/29/ubuntu%E9%85%8D%E7%BD%AE%E6%8C%87%E5%8D%97/)。当然现在Win的Linux子系统也很不错，后续可能会有介绍，大家可以期待一下。
