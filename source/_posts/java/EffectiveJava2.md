title: (2)、对于所有对象都通用的方法
date: 2016-03-09 21:03:05
categories: [Effective Java笔记]
tags: [effect java,对象]
---

Object的设计主要是为了扩展。它的非final方法（equals、hashCode、toString、clone、finalize）都有明确的通用约定（general contract）。任何一个类覆盖这些方法都有责任遵守这些约定。<!--more-->

# 覆盖equals时遵守通用约定

覆盖equals方法看似简单却易出错。最容易避免的方法就是不覆盖equeals方法，即类的每个实例都只与它自身相等。

# 覆盖equals时总要覆盖hashCode

如果覆盖了equals则必须覆盖hashCode。相等的对象必须有相等的散列码（hashCode）
# 始终要覆盖toString

Object提供的toString()返回的字符串通常不是期望的看到的。覆盖toString无论是否指定返回的字符串格式应在文档中说明。

# 谨慎地覆盖clone

因为Cloneable接口有很多问题，为了继承而设计的类不应该去实现Cloneable接口。

# 考虑实现Comparable接口

类实现Comparable接口，可以跟generic algorithm(泛型算法)及依赖于该接口实现的集合进行协作。

compareTo方法的通用约定与equals方法相似

通过指定返回值大小进行排序等操作