---
title: 'Hello, OS world!'
date: 2018-07-20 01:13:34
tags: 操作系统
---

> 从0开始到"Hello, OS world!"

这篇文章主要涉及: 

- 如何安装VirtualBox 、Ubuntu、NASM 、Bochs 
- 如何生成虚拟软盘
- 如何将引导扇区写入软盘，即运行一个超超超超小的“操作系统” 

这一系列过程以及其中遇到的一些bug。废话不多说，开始吧！



# 安装工具

> 工欲善其事，必先利其器。NASM，全称Netwide Assembler，是一款基于英特尔x86架构的汇编与反汇编工具。它可以用来编写16位、32位（IA-32）和64位（x86-64）的程序。 NASM被认为是Linux平台上最受欢迎的汇编工具之一。Bochs，主要用于操作系统开发（当一个模拟操作系统崩溃，它不崩溃主机操作系统，所以可以调试仿真操作系统）和在主机操作系统运行其他来宾操作系统。（来自维基百科）

## 1. 在Mac上使用VirtualBox虚拟机安装Ubuntu

我完全参照了[在Mac上使用virtualbox虚拟机安装ubuntu](https://blog.csdn.net/shenhaifeiniao/article/details/69397000)

其中第三点**主机与虚拟机文件夹共享**在后来的操作中也非常有用

## 2. 在Ubuntu上安装Bochs

不要用命令行直接安装，要去官网下载之后才能调试。

1. 在Ubuntu中进入[Boths官网](http://bochs.sourceforge.net/)，首页中有关于最新版的信息，点击SourceForge Project Page

2. 然后就出现了一大堆东西，该下载哪个呢？从书上之后的代码来看，应该是下载`boths-版本号.tar.gz`，我的是`nasm-2.6.9.tar.gz`

3. 下载完毕后打开终端，总共只有5条命令，第一条是：

   `tar vxzf boths-2.6.9.tar.gz的绝对地址`

   绝对地址可以直接拖那个文件进终端，我的是

   `tar vxzf /home/chenyulei/下载/bochs-2.6.9.tar.gz`

4. 继续在终端输入第二条命令：

   `cd bochs-2.6.9`

   在主目录新建一个名为bochs-2.6.9的文件夹并打开

5. 继续输入第三条命令：（**记得以后重新输入的时候一定要在bochs-2.6.9目录下，就是要cd bochs-2.6.9一下**）

   ` ./configure --enable-debugger --enable-disasm`

   这一步是为了打开调试功能的开关，但会出现一些bug：

   - Bug1：`configure: error: no acceptable C compiler found in $PATH`

     解决方法：首先输入`cd`回到主目录（不知道是否必然但是最好这样做一下，之后的bug也是，就不再提醒了），然后输入`sudo apt-get install gcc`

   - Bug2：`configure: error: C++ preprocessor "/lib/cpp" fails sanity check`

     解决方法：`sudo apt-get install build-essential`

   - Bug3：`ERROR: X windows gui was selected, but X windows libraries were not found.`

     解决方法：`sudo apt-get install xorg-dev`

   - Bug4：
		```
fatal error: X11/Xlib.h: No such file or directory
fatal error: X11/extensions/Xrandr.h: No such file or directory
		```

     解决方法：`sudo apt-get install libx11-dev libxrandr-dev`

6. 直到没有error后，输入第四条和第五条命令：

   ```
   make
   sudo make install
   ```

## 3. 在Ubuntu上安装NASM

1. 在Ubuntu中进入[NASM官网](https://www.nasm.us/)，在首页的Latest Version中选择The latest stable version，我的是2.13.03

2. 然后又出现了一大堆东西，依然下载`nasm-版本号.tar.gz`，我的是`nasm-2.13.03.tar.gz`

3. 下载完毕后打开终端，总共只有5条命令，分别是

   ```
   tar vxzf nasm-2.13.03.tar.gz的绝对地址

   cd nasm-2.13.03

   /.configure

   make

   sudo make install
   ```

   

# 将源程序编译成可执行文件制作启动盘

该部分参考了https://blog.csdn.net/friendley/article/details/51398336

## 1. 使用NASM编译源文件

- 在安装ubuntu时准备的共享文件夹这时候派上用场了，在mac os上把下载好的书上的代码复制到共享文件夹中，在ubuntu中的共享文件夹中打开终端，输入`nasm boot.asm -o boot.bin`
- 将boot.bin和bootsrc移动到bochs-2.6.9目录下

## 2. 生成虚拟软盘

- 依次在终端输入`cd bochs-2.6.9`、`bximage`
- 依次操作：
  - 输入1，回车
  - 输入fd，回车
  - 接下来都回车（默认输入方括号中的内容）

## 3. 将引导扇区写进软盘中

- 此时软盘和编译好的源码都准备好了，并且都放在了bochs目录下，在终端输入：

  `dd if=boot.bin of=a.img bs=512 count=1 conv=notrunc`

- 在bochs-2.6.9目录中找到bothscr并打开，修改其中内容为：

  特别注意：第9、10、13行的地址要修改为你的文件的绝对地址，在文件上右键属性可以查看父文件夹地址。

  ```
  ###################################################################
  # Configuration file for Bochs
  ###################################################################
   
  # how much memory the emulated machine will have
  megs: 32
   
  # filename of ROM images
  romimage: file=/home/chenyulei/bochs-2.6.9/bios/BIOS-bochs-latest, address=0xfffe0000
  vgaromimage: file=/home/chenyulei/bochs-2.6.9/bios/VGABIOS-elpin-2.40
   
  # what disk images will be used
  floppya: 1_44=/home/chenyulei/bochs-2.6.9/a.img, status=inserted
   
  # choose the boot disk
  boot: floppy
   
  # where do we send log message?
  log: bochsout.txt
   
  # disable the mouse
  mouse: enabled=0
   
  # enable key mapping, using US layout as default
  keyboard: type=mf, serial_delay=200, paste_delay=100000
  
  ```

- 在终端输入`bochs -f bochsrc`

- 依次操作：

  - 回车
  - 输入b 0x7c00，回车（在内存0x7c00处设置断点）
  - 输入c，回车（继续执行，直到遇上断点）
  - 输入c，回车

如果bochs中出现了红色的Hello, OS world!那么恭喜你成功了。

# 附：部分Bochs调试指令

| 行为                        | 指令                                |
| --------------------------- | ----------------------------------- |
| 在某物理地址设置断点        | b addr                              |
| 显示当前所有断点信息        | info break                          |
| 继续执行，直到遇上断点      | c                                   |
| 单步执行                    | s                                   |
| 单步执行（遇到函数则跳过）  | n                                   |
| 查看寄存器的信息            | info cpu 或 r 或 fp 或 sreg 或 creg |
| 查看堆栈                    | print-stack                         |
| 查看内存物理地址内容        | xp /nuf addr                        |
| 查看线性地址内容            | x /nuf addr                         |
| 反汇编一段内存              | u start end                         |
| 反汇编执行的每一条指令      | trace-on                            |
| 每执行一条指令就打印CPU信息 | trace-reg                           |

