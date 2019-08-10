---
title: ubuntu编译内核
date: 2019-07-22 15:33:25
tags: [linux,编译内核]
categories: 学习
toc: true
cover: /images/cover/04.jpg
---
最详细的Ubuntu编译内核教程
<!-- more -->
# 编译内核
## 1.先去官网下载内核源码
[Linux官方内核网址](https://www.kernel.org)
尽量选一个比自己内核版本高的内核去下载，尽量下载稳定版，稳定版不用打补丁，下好编译完成就能用。对于不知道自己内核版本号的童鞋，可以在Ubuntu下打开终端输入`uname -r` 
## 2.保存一下文件
最好是保存一下`boot/grub/grub.cfg` 这个文件他负责启动你的Ubuntu系统，你编译好内核之后是要把新内核信息加入到这个文件里面，这样你才能在启动Ubuntu的时候运用你的新内核。这个文件也是极其重要的。如果你在编译内核过程中操作不当，将里面的信息破坏了，那么你的系统将无法启动，这样你只能通过恢复这个文件来解决。
## 3.安装一下编译内核需要的一些程序
建议安装之前先更新一下软件来源，不然可能会出现安装失败的情况
更新操作:

```
         sudo apt-get update
         sudo apt-get upgrade
         
```
然后就是下载安装一系列的软件，为编译内核做准备

```
 sudo apt-get install libncurses5-dev   openssl libssl- dev 
 sudo apt-get install build-essential openssl
 sudo apt-get install pkg-config
 sudo apt-get install libc6-dev
 sudo apt-get install bison
 sudo apt-get install flex
 sudo apt-get install libelf-dev
 sudo apt-get install zlibc minizip
 sudo apt-get install libidn11-dev libidn11

```
## 4.解压内核到指定位置

```
	tar -xavf  linux-5.0.3.tar.xz  -C /usr/src 
```
其中内核版本为你自己下载的 准备编译的版本 后面的都是这样（我的是5.0.3 你的以你自己下载的为准）。记住去你下载内核的目录下去解压，不然会找不到文件。
## 5.进入到解压位置

```
cd  /usr/src/linux-5.0.3
```
后面的操作都是在这个目录下执行，如果不是必须我会说的。
## 6.净化源码

```
make mrproper
```
得到源代码后,将其净化。
## 7.对内核选项进行配置
得到内核之后你肯定要对其进行配置，不然最后和你的电脑不适配，也是有问题的。我进行配置的办法是将现有内核的配置选项复制到你要编译的新内核上去。首先，我们要把原来内核的选项配置复制出来`cp /boot/config- 4.15-23-generic  ./.config` 这句话的意思是将我现有的内核（4.15-23）版本的config配置信息复制到现在目录下的 .config里面 最后通过`make menuconfig ` 对内核选项进行配置。 这个就是根据上面那个.config来配置的。这个是一个图形化配置 选择 `load→OK→Save→OK→EXIT→EXIT`就OK了 ！ 注意看load后默认.config。
## 8.删除配置时留下的一些不用的文件

```
make clean  

```
第一次编译不需要使用，主要是多次编译时使用。清除上一次配置留下来的不用文件。
## 9.编译内核

```
make bzImage -jn
```
现在开始编译内核，时间会比较长，建议在电量充足的条件下进行。后面那个n是可以选择的进程数。意思就是多个线程一起编译。速度会较快一点。具体本机的线程数可以在设置里面查看详细信息。 
## 10.编译模块
这也是编译内核里面重要且耗时较长的一步具体同上一步。

```
 make modules -jn
```
## 11.安装模块

```
make modules_install
```

执行本操作 此时`/lib/modules/`下应该新生成一个新内核版本号的文件.
此时在新内核目录下的操作都已经做完了。编译安装都已经完成，只剩最后的收尾工作。将新内核添加到启动项里面。
## 12.将3个文件拷贝到boot目录下

```
mkinitramfs /lib/modules/5.0.3 -o /boot/initrd.img-5.0.3-generic
cp /usr/src/linux-5.0.3/arch/x86/boot/bzImage    /boot/vmlinuz- 5.0.3-generic
cp  /usr/src/linux-5.0.3/System.map    /boot/System.map-5.0.3  
```
如果不能执行 是缺少root权限的原因。在代码前面加上`sudo`。因为我下载的内核是5.0.3版本。具体你自己的版本是多少，你自己的命令就是多少，不要和我一样。

嗯~~命令敲到这儿....
你离成功只差0.0001了。最后更新一下grub。再查看一下grub.cfg里面有没有多出新内核的信息就可以了。


## 13.最后一步，更新grub
首先进入`/boot/grub/` 然后执行`update-grub2` 这时候你查看一下当前目录下的`grub.cfg`文件内容。如果发现里面多了新内核的信息。那么恭喜你！ 可以重启进入新内核了。


en~~最后重启进入系统的时候记得点一下高级选项，查看一下内核启动顺序。（因为你现在拥有不止一个内核了），可以自己选择内核进行启动。

## 14.小结
当你进入你的新内核的时候可能发现没有什么变化，这是肯定的。因为只是内核启动变了。输入`uname -r`查看一下现内核的版本号就OK了！

我自己呢，是编了2次内核才成功的。第一次是彻底将我的原内核和现内核的配置都搞坏了。那样我彻底进不去系统。根本无法修复。只能重装系统。第二次才成功。花了我2个下午的时间（编译内核是非常耗费时间的一个事情，建议如果没有什么必要的话就不要去弄他）。

来自木又的第二篇博客！
 
