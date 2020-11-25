# HTML和CSS的使用总结

本文从**语言和功能**两方面对HTML和CSS的使用进行总结：

![image-20200730082602852](/Users/bytedance/Library/Application Support/typora-user-images/image-20200730082602852.png)



## 一、HTML的使用

### 语言方面

这部分从文本实体和命名空间对HTML的使用展开讨论：

**文本实体**

```
&lt: --> <
&nbsp: --> 空格
&gt: --> >
&amp: --> 和号
&apos: --> '
&quot: --> "
```

**命名空间**

命名空间是通过`html`标签的`xmlns`属性设置的（即xml的namespace），如下：

```
<html xmlns="http://www.w3.org/1999/xhtml">
```



### 功能方面

HTML的功能体现在标签方面，分类如图1.1：

<img src="/Users/bytedance/Library/Application Support/typora-user-images/image-20200813100806995.png" alt="image-20200813100806995" style="zoom:50%;" />

<center>图1.1 html标签分类</center>

**元信息标签**

```
head meta title base(慎用) 
```

**语义类标签**

```
https://time.geekbang.org/column/article/78168
```

**链接型标签**

```
//链接型元素使用href标签
link a area
```

![img](https://static001.geekbang.org/resource/image/ca/51/caab7832c425b3af2b3adae747e6f551.png)

**替换型标签**

```
// 替换型元素使用src
script、img、picture、audio、video、iframe
```

**表格**



**表单**



## 二、CSS的使用

先看一下CSS的**语言方面**，分规则和单位两方面，规则分为普通规则和@规则。

### 语言方面

#### 普通规则

我们将普通规则分成**选择器和声明列表**，声明列表就是大括号内由属性和值组成的那些，看下面例子：

```css
.itemText { //.itemText就是选择器
    color: #fff; // 这两条就是声明列表
    font-size: 12px;
}
```

实际上的选择器和声明列表要复杂得多，

```
选择器：
	- 简单选择器
		- 标签
		- id class
		- 伪类
		- 伪元素
	- 复合选择器
		- 是指将多个简单选择器拼接起来用
	- 复杂选择器
		- 在复合选择器的基础上，使用combinator连接，比如空格 > + ~
	- 列表选择器
		- 比如使用 , 
```

```
声明列表：
	- 属性是由字母、下划线、中划线组成的标识符，但一定不能是两个中划线开头
	- 值是由字符串、关键字、数字、函数组成
	- 常见的用函数作为值的有：url calc max min clamp toggle attr
```

#### @规则

@规则算是css高级特性了，常见的有:

```
@charset  // @charset "utf-8";
@import // @import "xxx.css"
@namespace
@media
@keyframes
@fontface
@viewport
@support
@page
@counter-style
```

#### 单位



### 功能方面

CSS的功能主要体现在三个部分：布局、绘制、交互。

#### 布局

分为正常流和非正常流布局，前者包括正常流、float、Table、Grid布局，后者主要包括flex弹性布局、绝对定位。

**正常流**



**flex布局**

https://zhuanlan.zhihu.com/p/25303493

#### 绘制

绘制主要从颜色、字体以及形状展开讨论，

**颜色**



**字体**



**形状**



#### 交互

交互主要是动画，分为animation和transition

**transition**



**animation**