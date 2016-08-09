title: Android之序列化浅析
date: 2016-04-06 16:49:33
categories: [Android]
tags: [序列化]
---

# 序列化是什么

序列化是指**把Java对象转换为字节序列并存储到一个存储媒介的过程**。反之，**把字节序列恢复为Java对象的过程**则称之为反序列化。<!--more-->

# 为什么要序列化

Java对象存在的一个前提是JVM有在运行，因此，如果JVM没有运行或者在其他机器的JVM上是不可能获取到指定的Java对象的。而序列化操作则是把Java对象信息保存到存储媒介，可以在以上不可能的情况下仍然可以使用Java对象。
所以，序列化的主要作用是：
- 永久性保存对象，保存对象的字节序列到本地文件中；
- 通过序列化对象在网络中传递对象；
- 通过序列化在进程间传递对象。

# Android中序列化

在Android开发中，经常需要在多个部件(Activity、Fragment或Service)之间通过Intent传递一些数据，如果是一些普通类型的数据可以通过PutExtra()进行传递，如果是对象的话就得先进行序列化才能传递了。在Android中有两种序列化的接口，Serializable和Parcelable。

- Serializable：**(JavaSE本身就支持的)**保存对象的属性到本地文件、数据库、网络流、rmi以方便数据传输。
- Parcelable：**(Android特有功能)**因为Serializable效率过慢，为了在程序内不同组件间以及不同Android程序间(AIDL)高效
的传输数据而设计，**这些数据仅在内存中存在。**

## 何时使用它们

Parcelable的效率会比Serializable高，数据仅存在于内存中。；而Serializable因为使用到了反射，会相对慢一些，因此，只在内存间传递数据的话推荐用Parcelable，而如果是要进行保存或者网络传输则选择Serializable。

## Serializable接口的实现

只需要实现Serializable接口，并提供一个序列化版本id(serialVersionUID)即可。
```
public class DataBean implements Serializable{
	...
}

```

## Parcelable接口的使用

Parcelable实现方式略复杂一些，需重写describeContents和writeToParcel这两个方法提供一个名为CREATOR常量。实际上就是将如何打包和解包的工作自己来定义，
而序列化的这些操作完全由底层实现。
```
public class DataBean implements Parcelable{
    private int id;
    private String name;
    private String Account;
    private int kind;
    private String password;
    private String desc;

    public DataBean(){}
    // 用来创建自定义的Parcelable的对象
    public static final Creator<DataBean> CREATOR = new Creator<DataBean>() {
        @Override
        public DataBean createFromParcel(Parcel in) {
            return new DataBean(in);
        }

        @Override
        public DataBean[] newArray(int size) {
            return new DataBean[size];
        }
    };

   //GET SET方法
   ...

    @Override
    public String toString() {
        return "DataBean{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", Account='" + Account + '\'' +
                ", kind=" + kind +
                ", password='" + password + '\'' +
                ", desc='" + desc + '\'' +
                '}';
    }

    @Override
    public int describeContents() {
        return 0;
    }
    // 写数据进行保存
    @Override
    public void writeToParcel(Parcel dest, int flags) {
        dest.writeInt(this.id);
        dest.writeString(this.Account);
        dest.writeString(this.name);
        dest.writeString(this.password);
        dest.writeInt(this.kind);
        dest.writeString(this.desc);
    }
    // 读数据进行恢复
    protected DataBean(Parcel in) {
        this.id = in.readInt();
        this.kind = in.readInt();
        this.password = in.readString();
        this.name = in.readString();
        this.Account = in.readString();
        this.desc = in.readString();
    }
}
```
这样就完成了对DataBean的序列化，使用的时候就可以通过Intent进行传递了。
```
//Activity传递对象,不管是实现哪个接口都是用如下方式传递
intent.putExtra("data",data);

//在另一个Activity中接收对象的方式分别是：
DataBean data = getIntent().getSerializableExtra("data");
DataBean data = getIntent().getParcelableExtra("data");

```
Android中序列化的使用大致就是这样了。