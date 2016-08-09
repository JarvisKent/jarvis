title: 关于hexo的一些配置
date: 2015-12-24 09:38:09
tags: [hexo]
categories: [Hexo]
---
hexo博客搭建完成后，也clone下别人做好的主题使用，接着就会想修修补补，把想要的功能加上去，把不合自己口味的地方改改。在不断尝试下，也渐渐摸索出了一些门路。记录下来供参考<!--more-->

修改主题
----
找到想要的主题后，下载到目录的themes下
并修改全局config.yml中的theme;
如这边我下载的是jacman主题
```
git clone https://github.com/wuchong/jacman.git themes/jacman
```
修改全局的config.yml文件
theme:landscape  把landscape改为pacman
然后进行主题更新
```
cd themes/jacman
git pull
```
再通过
```
hexo clean
hexo d -g
```
上传到github即可。

添加about标签
----

在blog目录下使用命令

```
hexo new page about
```

会看到再source中生成了一个about文件夹，里面包含一个index.md。在这个文件内，我们可以编写一些about的信息。然后，进入themes/jacman(进入你使用的主题下)打开_config.yml，在menu中添加about的链接。
```
menu:
  首页: /
  归档: /archives
  关于: /about
```

这样就会再页面菜单中生成一个关于about的链接了。

添加categories分类和tags标签
----

参考jacman的[配置指南](https://github.com/wuchong/jacman/wiki/%E9%85%8D%E7%BD%AE%E6%8C%87%E5%8D%97)的设置。
首先是创建

```
hexo new page categories
hexo new page tags
```

接着分别进入blog目录下的source的categories（tags）的index，修改内容为

```
layout: categories(tags)
title: categories(tags)
```

这样就可以在新建的博文中使用标签了，如：

```
title: 使用hexo生成博客
date: 2015-12-22 13:30:48
categories: [hexo]
tags: [hexo] #多个标签的写法是[hexo1,hexo2]
```

当然，也可以在themes使用的主题中的.config.yml中的menu中添加

```
分类: /categories
标签: /tags
```

这样就会在菜单中增加分类和标签两个链接了。

添加多说评论
----

注册完多说后，在themes/主题下的.config.yml中修改一下字段:

```
#### Comment
duoshuo_shortname: jarviskent  #在这里添加你注册多说时填的shortname   
disqus_shortname:    
```

草稿（私密文章）
----

我们可以在blog的source下建一个draft文件夹，用于保存不舍得删除的。但也不想在页面上显示的文章，或者比较私人的不想让其他人看到的文章。
在source下创建一个_drafts文件夹即可。如果要强制预览，可以在blog目录下的.config.yml中强制开启。修改

```
render_drafts: false #强制预览修改为true
```

RSS订阅
----

添加rss订阅，需要安装插件

```
npm install hexo-generator-feed --save
```

接着在blog目录下的config.yml中添加

```
rss: /atom.xml #rss地址  默认即可
```

这样，rss订阅功能就开启了。

添加404页面
----

在themes的主题目录下，进入source文件夹里，添加404.html文件，内容为：

```
  <html>
  <head>
    <meta charset="UTF-8">
   <title>Jarvis's blog | 404</title>
  </head>
  <body>
  <script type="text/javascript" src="http://www.qq.com/404/search_children.
  js"   charset="utf-8"></script>
  </body>
  </html>
```

保存即可。

添加站长统计
----

我添加的是cnzz的站长统计，进入[cnzz](http://cnzz.com)进行注册和相应的设置后，在给出的代码中复制出id，把id填入到主题里的config.yml中对应的cnzz位置，如下

```
cnzz_tongji:
  enable: true
  siteid: 1257040183  
```
即可。

除此以外，我们也可以通过修改主题的_config.yml来添加一些诸如联系方式，友情链接等，也可以替换主题里的图片，图标等。如果想让主题更有个性化的话，也可以对主题内具体的内容部分进行修改，修改前记得进行备份，以防万一。具体实现参考该主题的[wiki](https://github.com/wuchong/jacman/wiki)。


