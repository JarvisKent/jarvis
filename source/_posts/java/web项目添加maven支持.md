---
title: web项目添加maven支持
date: 2016-09-24 16:49:05
categories: [java]
tags: [maven]
---
项目开发了一段时间决定要加入maven的支持，然后就找到了下面的方法。<!--more-->

# 方法之一

右键点击项目，选择Maven4MyEclipse 添加maven依赖，MyEclipse就会自动配置好maven并生成pom.xml。（因为MyEclipse中的maven被我改成了最新版才有这个选项，MyEclipse自带的maven可能没有该选项）
# 方法之二
对项目添加maven需要以下几个步骤。


- 在项目根目录下的.project文件中的<buildSpec>标签下添加如下代码

```
<buildCommand>
    <name>org.maven.ide.eclipse.maven2Builder</name>
    <arguments></arguments>
</buildCommand>
```

- 在项目根目录下的.project文件中的<natures>标签下添加如下代码

```
<nature>org.maven.ide.eclipse.maven2Nature</nature>
```

- 在.classpath文件的<classpath>标签下添加如下代码：
```
<classpathentry kind="con"
path="org.maven.ide.eclipse.MAVEN2_CLASSPATH_CONTAINER"/>
```

- 在项目的根目录下添加pom.xml文件（直接复制别的项目的pom.xml文件需要修改pom.xml中的**groupId和artifactId**）

# 方法之三

新建一个maven的项目，比对一下.classpath和.project两个文件内容，将其中和maven相关的依赖copy过去到对应的标签下再添加一下pom.xml文件就可以了。

接下来，又可以愉快的使用maven玩耍了。