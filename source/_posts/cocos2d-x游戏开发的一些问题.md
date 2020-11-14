---
title: cocos2d-x游戏开发的一些问题
date: 2017-06-19 18:05:27
updated: 2017-06-19 18:05:27
categories: Skills
tags: Cocos2d-x
index_img: /assets/cocos2d-x.png
---


## cocos2d-x安卓移植问题


### 未找到文件夹 'android-13'


```
编译模式：debug
使用 Eclipse 工程：D:\test1\proj.android
在 Android SDK的platforms下未找到文件夹 'android-13'，请使用 --ap 指定需要的API
版本。
D:\test1\proj.android>
```
解决方法：在sdk目录中找到platforms，里面会有你安装的sdk版本。我的是android-25，重新在命令窗中输入```cocos compile -p android --ap android-25```


### Leaving directory 执行命令出错，返回值：2


```
make.exe: Leaving directory `D:/test1/cpp-tests/proj.android-studio/app'
执行命令出错，返回值：2。
```
解决方法：如果是在proj.android中打开的命令窗口，则进入该目录中的jni，打开android.mk，修改代码添加class中的所有.cpp文件，一种方法是一个一个地敲进去，在
```
LOCAL_SRC_FILES := hellocpp/main.cpp \
			../../Classes/AppDelegate.cpp \
```
后面接着加上Classes中的所有.cpp文件（不要.h），我的是这样的
```
LOCAL_SRC_FILES := hellocpp/main.cpp \
			../../Classes/AppDelegate.cpp \
			../../Classes/Article.cpp \
			../../Classes/BaseAI.cpp \
			../../Classes/BaseFSM.cpp \
			../../Classes/BaseRole.cpp \
			../../Classes/BaseTrap.cpp \
			../../Classes/Bullet.cpp \
			../../Classes/Coin.cpp 
```
另一种方法是将
```
LOCAL_SRC_FILES := hellocpp/main.cpp \
			../../Classes/AppDelegate.cpp \
```
替换为
```
FILE_LIST := hellocpp/main.cpp    
FILE_LIST += $(wildcard $(LOCAL_PATH)/../../Classes/*.cpp)    
LOCAL_SRC_FILES := $(FILE_LIST:$(LOCAL_PATH)/%=%) 
```
看似第二种方法更简单一些，而且以后添加或者删除类都不用修改，但我一同学用这种方法修改.mk生成了apk后安装后却运行不了，她换成第一种方法之后就成功了。我用的第二种方法，但是并没有出现问题。


### Invalid or unsupported command

```
Invalid or unsupported command "update project -t android-25 -p 
```
如果你使用的android是20以上的版本，就有可能会出现这个问题。解决方法：将sdk中的tools替换成http://forum.cocos.com/t/creator1-4-1-android/45151/7此链接的回答中蓝色孤舟提供的tools。


之后遇到了一些很简单的bug，比如图片资源用了中文名（就算这个资源你根本没有用到），做屏幕适配时要注释掉AppDelegate.cpp里的一些有关放大缩小因子的代码，否则无论是SHOW_ALL还是EXACT_FIT图片都还是原本的大小。关于屏幕适配推荐一篇博文http://blog.csdn.net/cm_do2/article/details/50038851

祝大家移植成功~


## 瓦片地图（tiled map）问题

 - 一个图块层只能用一套瓦片素材！！也就是说如果你收集到的素材是离散的，但是又要用在同一图块层上，那你得先把素材都P到一张图上。我一开始做泡泡堂的时候就添加了很多图块，最后发现只显示出了最后添加的树，内心崩溃。。。

 - 地图遮挡的问题。做泡泡堂的时候会发现如果人物在第一层就会被建筑给挡住，如果人物在第二层就把建筑给挡住。。很尴尬。。解决方法是把建筑的上半部分给单独截出来，新建一个图块层来放这一部分（其实很多素材原本就是上半部分和下半部分分开的），就像这样![瓦片地图](https://img-blog.csdnimg.cn/img_convert/f665637805cc167008d71631471dc16d.png)

 - 走出边界的问题。我直接利用tiled解决的这个问题，在一开始设计地图的时候就把边界也留出来，比如15*13的地图，就做成17*15的，边界的地方用空的图块填充，我做泡泡堂的话将该图块的性质设置成遮挡且不能爆炸的就可以了。
