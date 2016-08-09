title: 1、Android体系与系统架构
date: 2015-12-27 10:40:43
categories: [Android群英传]
tags: [android,系统架构]
---

# Android系统架构

首先，我们要简单介绍一下Android的系统架构等内容，作为学习了一段时间的Android的再次回顾。先上一张Android系统架构的经典示意图。<!--more-->

![架构图](http://7xpi7i.com1.z0.glb.clouddn.com/android%E7%B3%BB%E7%BB%9F%E6%9E%B6%E6%9E%84%E5%9B%BE.jpg)

从这张图中，我们可以看到，Android大致分为了四层
- Linux Kernel（Linux内核层）
- Libraries 和Android RunTime（库和进行时）
- Applicaition Framework （框架层）
- Applicaitons （应用层）

Android的体系架构是鼓励系统组件重用，共享组件之间的数据，并定义组件间的访问权限控制。可以说，这些层次结构相互独立，又互相关联。

## Linux Kernel

Android最底层最核心的部分。我们打开手机设置 - 关于手机，可以看到一个内核版本，就是我们所用的Linux内核的版本。这一层包含了Android系统的核心服务，包括硬件驱动、进程管理、安全系统等等。

## Dalvik与ART

- Dalvik
包含了一整套Android运行环境的虚拟机。每个App都会分配Dalvik虚拟机来保证互相不受干扰，保持独立。特点是运行时编译。
- ART
从Android5.X开始，ART模式取代了Dalvik，它采用的是安装时编译，以后运行不用再编译。

## Framework

定义了一个应用程序运行所必须的全部功能组件,开发者也可以访问核心应用程序所使用的API框架。如果以后有打算研究Framework的具体流程，基本就是在和他们打交道。

## Libraries

开发者在开源环境中可以使用的开发库，以下是一小部分库的介绍：
```
SQLite:一个对于所有应用程序可用,功能强劲的轻型关系型数据库引擎。 
WebKit:支持Android浏览器和一个可嵌入的Web视图。 
Surface Manager:管理显示子系统,并且为多个应用程序提供2D和3D图层的无缝融合; 
```

## Application

App开发可以使用Java和NDK。不管是是哪种开发，它们都有Android Manifest文件、Dalvik Classes、Resource Bundle这几个东西。如果你有解压过APK的话，应该会注意到这些就是解压后的文件。对开发者来说，与Android系统最直接的接触就是SDK。所以，我们应当关注每个版本的SDK的修改，从而提高应用的兼容性。

Android的体系架构简单的说可以就只用这一张图来展示。学习初始，我们不需要多深入的理解这个体系架构，暂时只需要对它有个大概的认识就可以了。等掌握了使用方法后，再去慢慢了解它的原理，后面就会自然而然的清楚这个架构。

# Anndroid组件架构


我们做应用开发的话，比较关注的是应用层。这里简单介绍一下应用层的组件架构。应用层的APP组件架构，通常就是指Android四大组件，指的是
- Activity（活动）
- BroadCastReciever（广播）
- ContentProvider （内容提供者）
- Service (服务)


Activity是人机交互的第一界面，负责展示信息和处理结果。信息来源可以是资源获取、ContentProvider获取其它应用的信息，Service在后台处理、计算、下载的结果，或者是BroadCastReciever获取到的广播信息。Android系统提供了一个Intent作为信使，在各组件之间传递信息、交换数据等。四大组件因此而形成了各自独立又紧密联系的关系。它们根据不同的功能和特点使用于不用的地方，没有孰优孰劣，而我们需要做的就是熟知它们的功能、特点。


在这边，我们需要理解一个概念 —— Android上下文对象：可以理解为当前对象在程序中所处的一个环境，一个与系统交互的过程。Android的上下文对象即在Context中封装。Activity、Service、Application都是继承自Context。Android应用程序会在如下几个时间点创建应用上下文：
- 创建Application
- 创建Activity
- 创建Service


可以看出，创建Context的时机就是创建Context实现类的时机。应用程序第一次启动时，都会创建Application对象，同时创建Applicaiton Context，所有的组件共用这样一个Context对象。而创建Activity、Service系统也会创建相应的实例的Context对象。所以，我们在Activity中调用Context时可以直接使用this，而在其他匿名内部类使用时，则需要加上XXXActivity.this来获取指定Activity的Context对象。当然，我们也可以直接调用全局的Context，来进行整个应用上下文的引用。不过在某些时候，与某个组件的上下文引用还是用去别的。

# Android系统源代码目录和系统目录

## Android系统源代码


Android源代码的目录包含了Android系统所有的源代码，从底层驱动到上层应用，Android系统对所有文件进行了详细管理。在手机中，Android系统的目录与源代码目录不是一一对应的，而是与源代码编译后，与打包成的Image文件的结构相同。

查看Android源代码可以在[Android源代码](http://androidxref.com/)上进行查看或者也可以在Linux系统上直接进行编译。需要注意的是，Android源代码结构并不是一成不变的。Android作为手机操作系统，如果我们要使用它，就需要对源代码进行编译。在这方面，Android引入了Makefile机制（大型工程源文件不计其数，不同功能、模块按类型分别放在不同目录中，这些模块通常会有一个叫Makefile的文件进行管理。它就像一个shell脚本，不仅可以使用自己的语法，也能调用操作系统的命令。），它最大的好处是自动化编译，同时还可以做到可控制编译。如果没有Makefile，编译android源码将异常困难。

## Android系统目录

Android系统目录与源代码目录是有所不同的，我们可以通过ADB连接手机进行查看，部分目录如下，项目目录可查看[Android系统目录结构详解 ](http://blog.sina.com.cn/s/blog_61c5866d0100pnx1.html)
```
\system\app 这个里面主要存放的是常规下载的应用程序，可以看到都是以APK格式结尾的文件。在这个文件夹下的程序为系统默认的组件，自己安装的软件将不会出现在这里，而是\data\文件夹中。 
备注：这个目录是可以在ddms工具中看到的，当我们测试工作中某个应用出现修改需要验证时，可以直接在PC终端替换需要修改的模块的apk文件，就可以验证故障了，这种情况一般发生在研发不能确定你的故障现场而距离你又比较远的情况下。
\system\bin  这个目录下的文件都是系统的本地程序，从bin文件夹名称可以看出是binary二进制的程序，里面主要是Linux系统自带的组件(命令)
\system\customize  这个目录下主要是系统的设置 
\system\etc  从文件夹名称来看保存的都是系统的配置文件，比如APN接入点设置等核心配置。
 \system\fonts  字体文件夹，除了标准字体和粗体、斜体外可以看到文件体积最大的可能是中文字库，或一些unicode字库。
\system\framework  framework主要是一些核心的文件，从后缀名为jar可以看出是是系统平台框架。
\system\lib  lib目录中存放的主要是系统底层库，一些so文件，如平台运行时库。
 \system\media  \system\media\audio 铃声音乐文件夹，除了常规的铃声外还有一些系统提示事件音。
 \system\sounds  默认的音乐测试文件，仅有一个test.mid文件，用于播放测试的文件。
\system\usr  用户文件夹，包含共享、键盘布局、时间区域文件等。
```
查看方法可通过搜索引擎自行查询，网上有很多这方面内容，这边就不再进行介绍了。