---
layout: post
title: "Ubuntu安装、配置和使用"
subtitle: ""
author: "鹏少"
header-img: "img/image-20201229110142175.png"
header-mask: 0.2
tags:
  - Ubuntu
  - Linux
  - Shell
---

#### 为什么用Linux

- 基本不玩游戏，设计相关需求不多
- Linux适合编程，指几乎所有编程，甚至可以结合marpit，用markdown写Pre
- Linux——键盘党的福音
- Linux——不蓝屏
- Linux的操作逻辑完全不同，在熟悉后可以极大提高你对电脑的控制

###### 一些例子说明

- 把一个目录包括子目录下所有文件的文件名中的一个字符串替换掉

```bash
find . -name "*xxx*" -exec rename 's/xxx/yyy/' {} \;
```

- 把一个目录包括子目录下所有文件名带xxx的文件中的字符串zzz替换为yyy(yyy/后加上g，就是每行所有都替换)

```bash
find . -name "*xxx*" -type f -exec sed -i -e 's/zzz/yyy/' {} \;
```

- 把数据文件按行，每一百行切割一次（也可以按字节）

```
split xx.txt -l 100 yy.txt
```

- 获取txt数据文件前100行，并输出到一个新文件

```
head xx.txt -n 100 > xx100.txt
```

- 让电脑每隔10分钟自己输出当前ip，并上传更新到云盘

```
#crontab -e
*/10 * * * * /usr/bin/curl myip.dnsomatic.com > /path/to/seafile/pc.ip
```

- ...

#### 为什么是双系统

- 偶尔需要用Win剪视频
- 偶尔想要想要玩游戏
- 偶尔需要用PPT

> 当然，有两台电脑就可以不用双系统了:dog:

#### 为什么选择Ubuntu

- 用户群体最大
- 系统维护更好
- 遇到网上更容易找到解决方案

# ubuntu双系统安装

## 准备安装盘

#### 系统建议

个人用的话建议ubuntu20.04.3，最新的LTS版本。不过其他版本的安装大同小异。注意一点，ubuntu18.04及更高版本，才可以直接系统内大更新。

- 下载： 校内[tuna镜像网站](https://mirrors.tuna.tsinghua.edu.cn)，搜索ubuntu，点击ubuntu-releases，点击20.04.3，下载[ubuntu-20.04.3-desktop-amd64.iso](https://mirrors.tuna.tsinghua.edu.cn/ubuntu-releases/20.04.3/ubuntu-20.04.3-desktop-amd64.iso)

> LTS: *Long Term Support*, [长期支持版本](https://zhuanlan.zhihu.com/p/246313340)

### 刻录

- 需要准备一个空U盘（需要格式化）

#### 刻录到U盘（真$\huge\cdot$刻录）

- 刻录工具：推荐用[balenaEtcher ](https://www.balena.io/etcher/)


![image-20211105084225257](/img/inPosts/image-20211105084225257.png)


- 选择flash from file，然后找到你下载的iso文件；target选择你的U盘，然后flash即可。
- 优点：快捷

#### 通过ventoy工具

[ventoy](https://www.ventoy.net/en/index.html)可以把你的U盘变成一个多系统安装工具。安装任意系统，只需把对应的iso文件放入U盘的iso文件夹里。而且U盘剩下的空间还可以用来储存其余个人文件。

如果你经常需要装各种系统或使用PE，那么这样一个U盘可以满足你的所有需求。

具体下载和使用参考官网。你需要做的大致是这样: 下载ventoy*.exe，然后安装这个软件，然后进入这个软件，通过提示用这个软件来改造你的U盘。最后一步只需要把你下载的ISO放到U盘（ventoy）的iso目录下。

> 科服同学可以直接在nas上下载，老毕也写了详细的教程

## 安装前

- 关闭Win快速启动。控制面板->电源和睡眠->其他电源设置->选择电源按钮的功能->取消勾选启用快速启动。我的理解是开启这个，win关机了，但没完全关，因而会影响你的ubuntu使用。我遇到过的是，导致无法写入Win下的盘。
- 通过Win自带的磁盘分区或DiskGenius划出一块区域给双系统，并在Ubuntu分区右键点击删除卷。
- 重启进入BIOS关闭secure-boot
- 切换引导到安装盘->开始安装！

> 进入BIOS的快捷键一般是F2，切换引导快捷键一般是F12。如果不是上网查看即可。

## 系统安装

切换到安装盘引导后，会进行iso完整性检查，可以等它走完，也可以Ctrl+C跳过（一般不会有问题），

![image-20211103130437147](/img/inPosts/image-20211103130437147.png)

然后点击Install Ubuntu即可，

![image-20211103130633210](/img/inPosts/image-20211103130633210.png)

接下来是语言选择，建议就用默认的英语（中文的话后续使用会带来一些麻烦）。

![image-20211103130953309](/img/inPosts/image-20211103130953309.png)

然后是选择怎么装，如果想要用ubuntu自带的许多软件就点击Normal。但其实自带的很多不好用。建议选Minimal，已经包括了浏览器等基本软件。在其他选项里，建议取消勾选Download updates，可以在进系统后换一个镜像源再更新；一定要勾选Install third-party，这样会帮你装上合适的驱动。然后点击Continue即可。

![image-20211103131548992](/img/inPosts/image-20211103131548992.png)

然后是安装位置及分区，我是装虚拟机Ubuntu截的这些图。对于双系统，一般第一个是Install alongside with windows-boot-manager，通过这个选项，前面就可以不分区，然后它会自动帮你分区（只需要你在下一个界面选择给ubuntu多少空间），并自动把引导和windows的引导安装在一起。

不过保险起见，建议选择Something else，这个选项需要提前分一个Ubuntu的区。

![image-20211103131625993](/img/inPosts/image-20211103131625993.png)

然后会进入下面的界面，点到freespace,然后点击"+"，如下选择就行，接下来在Device for boot loader这一项，选择有windows-boot-loader字样的，然后点击Install即可。接下来就是较长的安装环节。

![image-20211103133034222](/img/inPosts/image-20211103133034222.png)

然后会进入用户名和密码设置阶段，建议密码不要设置太复杂，后面需要经常输入。接下来会提示你restart now，重启后会让你拔出安装介质然后回车，这些跟着走就行了。然后就装好了！

## 可能的问题

### BIOS相关

#### 老机器

- 对于较老的电脑，BIOS可能只有Legacy模式，这就比较麻烦了,似乎分区方式和安装流程都不太一样，我还没遇到过，参考下这个

	https://zhuanlan.zhihu.com/p/61426828


#### 其他

这里有一些其他相关项，我也没弄过，如果你引导不了试试吧

- USB UEFI BIOS Support 设置为 Enabled

- UEFI/Legacy Boot 设为 UEFI Only 

### 系统相关

进入后可以把软件源换成阿里云的镜像（不晓得为啥，清华源最近突然用不了了），然后可以更新下系统和软件。但一定要注意，有时候更新系统会导致内核和驱动不匹配，需谨慎。可以做的是，检查下dkms模块（在内核更新时适配驱动）有没有装好。

#### 开机黑屏

```
#未能开机前的方法
在grub2界面ubuntu选项上,按e,定位到有"quite splash"的行的末尾,空一格，然后加上 nomodeset。然后F10启动系统
# 注意，这里是用于Nvidia显卡，其他家的显卡我就不知道了

#开机后的方法
sudo gedit /etc/default/grub
# quiet splash修改为quiet splash nomodeset并保存
sudo update-grub
```

#### 系统报错

> 遇到过两次，但似乎没有什么要紧的问题，强迫症可按照下面方法解决

关掉提示，删掉crash文件，并禁用报告

```bash
sudo rm /var/crash/*

sudo vim /etc/default/apport
#Change the enabled=1 to enabled=0

sudo stop apport
```

> 可以参考：
>
> https://www.unixmen.com/system-program-problem-detected-fix-remove-ubuntu/
>
> https://itsfoss.com/how-to-fix-system-program-problem-detected-ubuntu/



# linux使用建议

## 一些逻辑

- 大部分事情都可以通过命令行——在习惯后会发现更高效
- 系统权限更加清晰可控，也要求使用者需要更加谨慎

<img src="/img/inPosts/OIP-C.lkSptDCBec9XK4IdgCBR3QHaHa" alt="sudo rm rf 的图像结果" style="zoom:150%;" />

- 一切皆文件，比如，磁盘的使用需要挂载，这里挂载相当于是把物理磁盘和一个可以操控的文件夹连接起来
- 大部分用需要用鼠标解决的问题你都有快捷键，以及其他解决方案
- 大部分你能想到的文本操作linux都自带
- 命令学习途径
	- utools——一个软件，通过安装linux命令这个插件可以快速查找一个命令的用法
	- xxx --help
	- man xxx
	- 搜索

## 一些命令

#### 文件和文件夹操作

- cd
- ls
- cp
- mv
- rm
- mkdir/rmdir
- rename
- z
- extract
- cmp
- find

#### 文本相关

- cat
- more
- less
- head
- tail
- grep
- diff

#### 系统工具

- mount
- wget
- ping
- ssh,scp

- rsync
- top
- ...

## 基础的配置建议

> 相比于原装系统，通过简单的配置，就可以将使用体验大幅提升

#### 更换系统软件源

最近Tuna的源不知为何不好用了，可以更换为[阿里云镜像](https://developer.aliyun.com/mirror/ubuntu?spm=a2c6h.13651102.0.0.3e221b11RVdn6n)

更换方法：

在镜像网站中选择ubuntu版本 ，复制框内所有。使用快捷键`ctrl+alt+T`进入命令行按如下流程操作：

```bash
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak#备份 
sudo gedit /etc/apt/sources.list  #换源,用前面复制的替换掉文件里的内容
sudo apt update #将更新传给apt（即软件管理器） 
sudo apt upgrade  #更新软件和内核 
sudo apt autoremove  #清除不再需要的依赖 
```

#### 搜狗输入法

- 需要打开language  support（win键盘查找），应该会提示你需要下载一些东西。检查下有没有中文，没有就点击install/remove languages安装它。
- 去官网上下载linux版本，下载好了直接双击安装。
- 再次进入language support，切换keyboard input method system 为 fcitx 。然后可能需要重启才能生效。然后进入fcitx configure，删掉现有的input method，然后点击"+"，取消框选"only show current language"，然后搜索sougou，添加上即可。

#### 微信

> 不过这个途径最近可能有问题，很玄学。遇到一个案例是各种deepin-wine的项目以及原生的wine都不行，但把系统升级到22.04就行了。

安装方法:

```bash
#1.添加仓库：
wget -O- https://deepin-wine.i-m.dev/setup.sh | sh

#2.安装微信：
sudo apt install com.qq.weixin.deepin
#3.安装QQ：
sudo apt install com.qq.im.deepin
```

#### 配置终端——oh-my-zsh

为终端配置补全、高亮等功能。

```bash
#安装zsh,git
sudo apt install git zsh

#切换到zsh
sudo usermod -s /bin/zsh username  #改为你的用户名

cd  #进入用户目录
git clone https://gitee.com/jklash1996/ohmyzsh.git .ohmyzsh
cd .ohmyzsh
cp templates/zshrc.zsh-template ../.zshrc #拷贝配置模板
zsh oh-my-zsh.sh  #安装ohmyzsh

#装另外两个插件
cd custom/plugins
git clone https://github.com/zsh-users/zsh-autosuggestions.git
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git
```

进入zsh的配置文件**.zshrc** (gedit ~/.zshrc), 找到**plugins=(插件)** 这句话，在括号添加这些插件（空格或空行隔开每个插件,一开始只有git插件）:

```bash
extract   #解压各种格式压缩文件
z #根据使用历史，比如xxx为一个路径的关键词，直接"z xxx"就可以跳转到该路径参考：https://github.com/rupa/z/
zsh-autosuggestions #补全
zsh-syntax-highlighting  #语法&高亮
```

然后退出文件保存，进入命令行执行:

```bash
source ~/.zshrc
#使以上配置生效
```

#### 一些小工具

- trash-cli(解决linux下rm容易误删除问题)

安装**trash-cli**：

```
1 sudo apt install trash-cli 
```

功能有：

> trash-put： 删除文件和目录（仅放入回收站中）
>
> trash-list ：列出被删除了的
>
> trash-restore：从回收站中恢复
>
> trash-rm：删除回收站中的
>
> trash-empty：清空回收站

为了方便使用将以下别名设置加入 **~/.zshrc**文件最后:

```bash
alias rm='trash-put'  
alias rml='trash-list'
alias rmr='trash-restore'
alias rmrm='trash-rm'
alias rme='trash-empty'  
alias rmt='/bin/rm'    #系统的rm,建议且只建议在批量删除或删除大文件时使用(速度较快)，其他时候都使用 rm 
```

- Screenshot(自带)，快捷键在设置（settings）里的key boards里找

- top，一般自带。用来查看服务器的后台、负荷的命令行工具，Linux系统自带。直接在命令行键入**top**，就会出现各种信息，按**q键**退出。

## linux重要配置文件

- /etc/fstab

用于挂载，如过需要用win下的盘，可以通过这个实现自动挂载，可以参考我的:

```bash
 /dev/nvme0n1p4 /mnt/data ntfs defaults 0 1
 #这里/dev/nvme0n1p4是我的数据盘的名字，可以通过sudo  fdisk -l查看
 #在这个之前需要先创建data这个目录
 sudo mkdir /mnt/data
```

- /etc/default/grub

用于自定义grub2，比如主题、以及自动保存

- /etc/profile

系统级的shell配置文件，但一般我不用，只用个人目录下的

- crontab -e

定时命令，比如定时把主机的ip同步到gitee上，然后你的其他机器就可以实时获取

- /home/username/.zshrc（或.bashrc）

用户级shell配置文件，比如设置别名（alias，把一些不想敲的命令缩短），比如

```bash
alias ab="conda activate base"
```

以及其他各种配置

## 进阶使用

#### Shell

最开始的例子只涉及皮毛，值得深入学习。由于小编也只会些皮毛就不多说了。

#### vim

- 文本操作利器
	- 宏替换
	- 快速定位
	- ...
- IDE——通过配置插件可以很好地实现大部分语言的IDE功能
	- 补全
	- 高亮
	- 查看函数定义
	- 调试

#### 各种快捷键-及自定义设置

不知道的搜索，然后孰能生巧。
