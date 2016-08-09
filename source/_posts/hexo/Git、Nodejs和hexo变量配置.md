title: Git、Nodejs和hexo变量配置
date: 2016-08-08 17:02:59
tags: [hexo]
categories: [Hexo]
---

其实是因为系统重装了，安装的Git和Nodejs得重新配置下环境变量，记录一下，省得每次要重新去查都要配哪几个变量<!--more -->

# 配置Git环境变量

在环境变量配置界面中，变量Path中配置Git的路径下的cmd；

![GIT环境配置](http://obl32g9cf.bkt.clouddn.com/git%E9%85%8D%E7%BD%AE.png)

如我的路径是

```
G:\git_node\Git\cmd
```

配置完后检测一下是否配置成功，在命令行中输入

```
git version
```

如果出现类似

```
git version 2.6.3.windows.1

```

的字样就是配置成功了，否则检查一下路径是否有出错。

git的全局用户名及邮箱配置

```
//修改GIT全局用户名
git config --global user.name [username]

//修改GIT全局邮箱
git config --global user.email [email]
 
```

# 配置Nodejs环境变量

Nodejs需要配置两个环境变量；
1、在path中配置nodejs.exe的位置,如我的路径是

```
G:\git_node\nodejs
```

2、新建一个变量名为NODE_PATH，参数是上面的路径在加上/node_modules,如

```
G:\git_node\nodejs\node_modules
```

配置好后的检测方法为
```
//输入 node -v 如果出现类似 v4.2.2 即nodejs配置成功
//输入npm -v 如果出现类似 2.14.7即 node_modules配置成功
```

# hexo也要配置 

git和nodejs都配置好了，输入hexo的时候还是不能使用，除了可能是没有安装hexo以外，还有可能是没有配置环境变量。

1、安装hexo
```
npm install -g hexo-cli
```

2、配置变量，也是在path中加入hexo的路径，如

```
C:\Users\Administrator\AppData\Roaming\npm\node_modules\hexo\bin\
```

配置完后输入

```
hexo version
```
如出现版本号，即配置成功了。

接下来，就可以继续愉快的写博文了！！！
