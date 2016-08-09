title: 2、Android开发工具接触及ADB命令
date: 2016-01-02 09:40:43
categories: [Android群英传]
tags: [android,Android Studio,ADB命令]
---
这一章，我们要了解一下Android的IDE（集成开发环境）。一款好的IDE不仅能让我们开发事半功倍，也能让我们把更多精力专注在业务逻辑本身，不必过多关注底层细节。<!--more-->

进行Android开发，目前主要是使用Eclipse和AndroidStudio。Eclipse需要整合adt插件才能进行Android开发，而AndroidStudio是Google推荐的专门用于Android开发的IDE。因为AndroidStudio是谷歌一手打造，并不留余力的完善它，而Eclipse已经停止更新；所以，我们这边主要说的是AndroidStudio。

# Android Studio的下载与安装

如果你能顺利穿墙的话，可以直接到[Android Studio官网](http://developer.android.com/sdk/index.html)进行下载，如果不行的话可以到[AndroidDevTools](http://www.androiddevtools.cn/)进行下载，包括SDK的更新也可以根据这个网站的方法进行配置。我是在[AndroidDevTools](http://www.androiddevtools.cn/)上下载的2.0Preview4版本和SDK安装包。

在Android安装之前，我们得先安装JDK并进行环境配置。配置完JDK后，并安装好AndroidStudio就可以开启了，一般第一次开启会进行一些更新，可能会比较久一些。网上有许多关于AndroidStudio的使用及配置方法，这边就不过多介绍了。接下来，我们主要想介绍的是ADB命令，如何通过命令连接手机和电脑。

# ADB命令使用技巧

## ADB配置

ADB——AndroidDebugBridge。是用于连接手机和电脑的工具，可以让我们用电脑操作手机。当我们安装完AndroidStudio后，会有个SDK目录，而ADB工具是位于SDK的platform—tools的目录下的。我们需要把这个目录添加到系统环境变量中才能直接使用。目前，AndroidStudio内置了终端，我们可以很方便的使用。

配置完环境变量后可以在命令行中输入

```
adb version
```

如果提示的是如

```
Android Debug Bridge version 1.0.32
```

就说明配置成功了，我们只要在手机上确认下对该电脑授权后就可以直接在命令行里对手机进行操控了。

## ADB命令的使用

- 开启和关闭ADB服务

```
adb start-server
adb kill-server
```

### shell命令

Android是基于Linux开发，所以，它也支持一些常用的shell命令。我们在命令行中输入

```
adb shell
```

然后，选择正在连接电脑的手机，就可以对其使用shell命令了。

### ADB常用命令

接下来，我们来看一些ADB常用命令。

```
//用于查看当前连接到电脑的所有Android设备
android list targets 

//用于安装apk安装包，即安装application到手机里。
adb install -r application.apk 

//用于安装apk到手机里，如adb push D:\Test.apk /system/app。是把Test.apk这个文件安装到手机的system/app目录下。
如果是作为普通用户的话，push主要是用于把手机上的文件拷贝到手机里。
adb push <local> <remote> 

//有把文件从电脑拷贝到手机里，就有从手机里拷贝到电脑上的命令，如下
adb pull <remote> <local>
```

作为开发者的我们，要使用的命令除了上面几个以外，还包括了一些用于调试或查看信息的命令，如

- 查看log

进入shell命令后，可以输入

```
adb shell
logcat|grep "abc"
```


- 删除应用

```
adb remount(重新加载分区，使系统分区重新可写)
adb shell
cd system/app
rm *.apk
```

- 查看系统盘符

```
adb shell df
```

- 输出所有已经安装的应用

```
adb shell pm list packages -f
```

- 模拟按键输入

```
adb shell input keyevent code
```

最后的code是指要执行的命令，可以再网上查询对于的code，常用的一些模拟命令如下

```
input keyevent 82 menu
input keyevent 3 home
input keyevent 19 up
input keyevent 66 enter
input keyevent 5 back
```

- 模拟滑动输入


```
adb shell input touchscreen <x1> <y1> <x2> <y2>

```

- 查看运行状态 

可以列出很多运行状态，具体可查看API文档。如Activity状态，同时过滤"tencent"关键字。

```
adb shell dumpsys
dumpsys activity activities | grep "tencent"

```

- Package管理信息

```
pm list packages -f

```

- AM管理信息

```
adb shell am start -n 包名+类名

```

- 录制屏幕

```
adb shell screenrecord/sdcard/demo.mp4

```

- 重新启动

```
adb reboot

```

### ADB 命令来源

adb命令主要存在于/system/core/toolbox和/frameworks/ase/cmds两个目录下。

总结：这一章，主要想把adb命令给记录下来方便以后使用查看。