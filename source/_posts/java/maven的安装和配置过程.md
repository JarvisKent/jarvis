---
title: maven的安装和配置过程
date: 2016-09-19 22:24:50
categories: [java]
tags: [maven]
---
# 一、maven的下载及环境变量的配置


本篇建立在不使用myeclipse自带的maven上，如果是使用自带的maven可直接看第四步。

## 1、下载

从[maven最新版](http://maven.apache.org/docs/3.0.3/release-notes.html)下载最新版的maven，并解压。如我的解压路径是

```
F:\maven\apache-maven-3.0.3
```


## 2、环境变量配置

我的电脑-----属性----高级-----环境变量-----环境变量-----新建
变量名：MAVEN_HOME
变量值：F:\maven\apache-maven-3.0.3（maven的路径）
找到path 
在环境变量值尾部加入：;%MAVEN_HOME%\bin;---前面注意分号

## 3、验证是否安装成功
- 安装成功
在命令行中输入mvn -v如果是正常弹出如下信息的话则证明安装成功。
![安装成功](http://obl32g9cf.bkt.clouddn.com/maven1.png)
- 安装出问题
 如果是报错了，弹出如下信息。
![安装失败](http://obl32g9cf.bkt.clouddn.com/maven2.png)

因为maven需要jdk为1.7及以上版本，所以把jdk改为1.7版本即可。

# 二、修改maven的本地仓库路径

## 1、修改本地仓库路径
我们知道，maven在进行包管理的时候，第一次使用会从中央仓库下载所需的包到本地仓库里，下次再使用的时候就会直接从本地仓库来加载所需的包了。而如果我们自己更换了maven的版本的话，则需要修改一下本地仓库的路径（默认注释掉了）。在maven目录下的conf/setting.xml中,如下图
![配置本地仓库](http://obl32g9cf.bkt.clouddn.com/maven3.png)
这样，我们加载的包都会被下载到该路径下，后期编译生成的jar包等，也都是在该目录下。

## 2、验证是否成功
输入命令：
```
mvn help:system 
```
如果没有报错的话，可以进入本地仓库的目录下，可以看到生成了一些文件。

# 三、更换MyEclipse中的maven
从MyEclipse的菜单栏点击Windows -> Preferences -> Maven  -> Installations，将之前解压的maven添加进来，如图所示
![配置maven](http://obl32g9cf.bkt.clouddn.com/maven4.jpg)

点击User Settings 使用我们自己的Maven配置，如图所示
![配置maven](http://obl32g9cf.bkt.clouddn.com/maven5.jpg)

# 四、在项目中使用maven

## 1、maven命令行创建项目

略
## 2、MyEclipse创建项目时添加maven

在MyEclipse创建项目的时候，可以直接加入maven的支持，如下图，只要勾选上add maven supprot即可。

![添加maven](http://obl32g9cf.bkt.clouddn.com/maven6.png)
 
 
