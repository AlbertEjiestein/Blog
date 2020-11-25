# HTML标签之绘制型

绘制型标签一共有两种：canvas和svg，确切的说，svg并不是HTML，可以将svg视为文档标记方言，它侧重于形状而不是文本。svg与canvas的区别在于：svg会保留图形的原始形状，是矢量的，canvas则不会，在画布上移动某个图形的唯一方法是清除画布，然后重新绘制。



## SVG

### 介绍



### 使用



## canvas

### 介绍

画布可以绘制在canvas标签上，可以给canvas标签设置宽高控制大小。

同时支持绘制二维和三维图形，比如二维中的线、面、路径、曲线、饼图、文本、图片和转换等，三维中的“webgl”可以做的动画。尤其是webgl，因为涉及了图形学知识，很重要。

### 使用

要使用canvas标签绘制各种图形，就得调用绘图接口，那就首先需要创建一个上下文(context)，这是一个对象，其方法提供绘图接口

```js
let canvas = document.querySelector("canvas");
let context = canvas.getContext("2d");//2d
// let context = canvas.getContext("3d");//3d
```

