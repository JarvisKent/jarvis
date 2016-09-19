---
title: 使用iframe标签时父窗体与子窗体之间的js调用
date: 2016-09-19 20:52:28
categories: [java]
tags: [iframe标签，js]
---
在使用iframe标签的时候，难免会需要在父窗口调用子窗口中的js方法；子窗口调用父窗口的方法；兄弟窗口之间方法调用，所以，稍微整理一下这方面的内容。<!--more -->
# 父窗口调用子窗口中的方法
1、父窗口中的iframe标签，id为childFrm。
```   
<iframe id="childFrm" name="childFrm" width="100%" scrolling="no"
            frameborder="0" onload="" height="750px" 
            style=""></iframe>  
```
2、子窗口中包含的方法
```
function aaa(){
    alert(“父窗口调用子窗口的测试方法”);
}
```
3、父窗口中的调用函数为
```
function test(){
   childFrm.window.aaa();   
}
```
如果是多个子窗口的话,通过id来获取
```
//1、通过id获取到iframe标签，在调用它包含的方法
document.getElementById("childFrm").contentWindow.aaa();

//2、用jQuery的话需要加[0]
$("#childFrm")[0].contentWindow.aaa(); 
```
# 子窗口调用父窗口的方法

1、父窗口包含的方法
```
function test(){
    alert("test");
}
```

2、子窗口调用方法

```
function childtest(){
    window.parent.test();
}  
```

# 兄弟窗体间调用方法

同级iframe页面之间调用，需要先得到父亲的window，然后调用同级的iframe得到window进行操作；
```
parent.$("#childFrm")[0].contentWindow.ff; 
```

# 传递参数到子窗体

子窗体childFrm中存在一个hellobaby参数的话，在父窗体可以用下面方法来实现传参
```
$("#childFrm")[0].contentWindow.hellobaby="test"; //可以通过这种方式向iframe页面传递参数，在iframe页面window.hellobaby就可以获取到值，hellobaby是自定义的变量；
```
