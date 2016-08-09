title: 使用hexo生成博客
date: 2015-12-22 13:30:48
categories: [Hexo]
tags: [hexo]
---
之前，也创建过一次博客。是通过百度别人的文章，跟着步骤一步一步来做的。过程中遇到的几个问题基本都是因为环境变量没配置好，以及命令顺序错误。虽然最后博客是搭建成功了，搭建过程却还是感觉不清不楚的，那次的创建，基本上只是起到了大概熟悉下git和github怎么使用。所以，就有了这篇文章。<!--more-->

以下内容建立在对git、github、node.js有一点了解的基础上。
软件的安装
----
我们需要安装[git](http://git-scm.com/download/)和[node.js](https://nodejs.org/en/)这两个软件，我不确定版本是否会有影响，我这边安装的是Git-2.6.3和node-v4.2.2这两个版本。安装完后记得要进行环境配置。
SSH key的生成
----
我们使用SSH key让本地git项目和github建立连接。这里需要几个步骤来实现：

**1、检测是否已经有SSH key**

```
cd ~/. ssh
```
如果已经存在，会进入SSH文件夹，key就在里面，可直接跳过第二步。

**2、生成SSH Key**

```
ssh-keygen -t rsa -C"邮箱地址@youremail.com"
```
出现Enter file in which to save the key (/Users/your_user_directory/.ssh/id_rsa):<回车就好>
接下去需要输入两次密码即可生成key。

**3、把key添加到github上**

登录github后点击Settings，选择SSH keys-Add SSH key；
用文本打开id-rsa.pub，复制里面的内容放置到key窗口里保存即可。
安装hexo-cli
----
安装并配置完软件以后，我们需要安装hexo。可以使用自带的命令台或者GIT BASH进行安装，输入命令：
```
npm install -g hexo-cli
```
在被墙的情况下，npm可能很难使用，这个时候可以考虑使用cnpm，cnpm需要安装，可参照[淘宝NPM镜像](http://npm.taobao.org/  )进行设置。
开始创建博客项目
----
hexo安装完后，就可以开始着手项目了，使用命令：
```
hexo init myBlog
```
创建一个项目后，进入项目
```
cd myBlog
```
这边需要先运行下面这个命令,没有运行这个命令的话，会卡在无法提交到github这一步上，没有补救的方法，只能重新来：
```
npm install hexo-deployer-git --save
```

接着运行
```
npm install
```
执行完到这边，我们的博客创建之路已经走了一大半。这边我们可以输入一下命令
```
hexo g
hexo s
```
然后在浏览器中登录[http://localhost:4000/](http://localhost:4000/)来查看是否成功生成了页面。
接下去再对全局的config.yml（有几个，我们这边修改的是创建的myBlog目录下的config.yml）进行配置后就可以上传到github上并进行访问了。
修改config.yml配置
----
用文本打开config.yml，在最末尾添加如下内容：
```
deploy:
  type: git
  repository: https://github.com/JarvisKent/jarviskent.github.io.git [改成你自己的仓库]
  branch: master
```
这边需要注意的是:后面的空格是必须有的，不能省略。
修改完保存后，输入如下命令
 ```
 hexo d -g
 ```
 接着会要求输入github账号和密码，输入后即可上传成功。
验证是否创建成功,打开浏览器输入jarviskent.github.io（仓库名）,即可验证是否创建成功。