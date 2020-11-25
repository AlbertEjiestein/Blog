# CSS语言：普通规则之声明列表

## 背景

声明列表是CSS的重点，我们涉及的**CSS属性和值**都属于**属性列表**，项目中遇到了一些属性，本文进行了总结。

## z-index

z-index是设置样式中很常用的一个属性，由于在最近的项目中有用到但不是很熟练，总结一下。

### 层叠上下文

<img src="https://user-gold-cdn.xitu.io/2018/9/21/165fc5323852bd61?imageslim" alt="img" style="zoom:50%;" />

层叠上下文是HTML中一个三维概念，层叠的方向是从屏幕向外。层叠上下文可以有很多个，每一个层叠上下文中又可以有很多层叠等级，类似于各BFC之间的元素互不影响，但确具有可以互相重叠的特性。

<img src="https://user-gold-cdn.xitu.io/2018/9/21/165fc53567526009?imageslim" alt="img" style="zoom: 67%;" />

**形成层叠上下文的方法有：**

+ 每一个网页都有一个层叠上下文，即`html`标签

+ 给一个`定位元素`赋予非auto的z-index值，也可以生成层叠上下文

  > 定位元素指：position为relative、absolute、fixed、sticky

+ `opacity`属性值小于1的元素

+ z-index不是auto的`flex元素`

--------------------以上是比较常用的，还有一些不常用的-------------------------

+ `transform` 属性值不为 `none`的元素
+ `mix-blend-mode` 属性值不为 `normal` 的元素
+ `filter`、`perspective`、`clip-path`、`mask`、`mask-image`、`mask-border`、`motion-path` 值不为 `none` 的元素
+ `perspective` 值不为 `none` 的元素
+ `isolation` 属性被设置为 `isolate` 的元素
+ `will-change` 中指定了任意 CSS 属性，即便你没有直接指定这些属性的值
+ `-webkit-overflow-scrolling` 属性被设置 `touch`的元素



总结：

1. 既然可以创建多个层叠上下文，那么之间有什么关系呢？个人觉得类似于“树”结构，有一个根元素的层叠上下文html，然后下面又包含了子层叠上下文，兄弟分支的上下文又互相不影响。
2. 而在同一个层叠上下文中，各元素之间是有层叠等级的，这个等级不是层叠上下文的等级，而是不同类型元素的等级，比如普通元素之间、普通元素与定位元素和浮动元素之间等等，接下来会讲。



### 层叠等级

层叠等级针对的是**同一个层叠上下文中**各个元素在z轴上的显示顺序，层叠等级不一定是由z-index决定的，它有以下特点：

+ 首先，层叠等级一定是要在同一个层叠上下文中才有意义

+ 然后，层叠等级的含义是该层叠上下文中各元素在z轴上的顺序

+ 另外，普通元素之间的层叠等级优先由其所在的层叠上下文决定，然后再是在HTML中出现的顺序

+ 最后，在同一个层叠上下文中会有定位元素，其层叠等级只由z-index决定

  > z-index 只适用于定位的元素，对非定位元素无效，它可以被设置为正整数、负整数、0、auto，如果一个定位元素没有设置 z-index，那么默认为auto



### 层叠顺序

层叠顺序和层叠等级一样，描述的是**同一个层叠上下文中**的顺序规则，共有七种顺序：

![img](https://user-gold-cdn.xitu.io/2018/9/21/165fc538e321d802?imageslim)



+ **背景和边框**：形成层叠上下文的元素的背景和边框。
+ **负z-index值**：层叠上下文内有着负z-index值的定位子元素，负的越大层叠等级越低；
+ **块级盒**：文档流中块级、非定位子元素；
+ **浮动盒**：非定位浮动元素；
+ **行内盒**：文档流中行内、非定位子元素；
+ **z-index: 0**：z-index为0或auto的定位元素， 这些元素形成了新的层叠上下文；
+ **正z-index值**：z-index 为正的定位元素，正的越大层叠等级越高；



### 实战

**巧用层叠上下文**

- 关键实现： 伪元素 层叠上下文
- 具体分析： 利用层叠上下文和 `z-index: -1` 特性实现伪元素覆盖背景同时又不会遮挡文字（具体实现原理参考[这里](https://juejin.im/post/5ce607a7e51d454f6f16eb3d#heading-28) ）。这是一个非常常用又好用的技巧，善加利用可以达到很多意想不到的效果。[地址](http://js.jirengu.com/nozih/2/edit?html,css,output)

![image-20200727184653421](/Users/bytedance/Library/Application Support/typora-user-images/image-20200727184653421.png)

**边框内圆角**

- 关键实现：伪元素 层叠上下文
- 具体分析：利用伪元素实现圆角矩形并叠加在父元素的背景之上文字之下：[地址](http://js.jirengu.com/dikux/1/edit)

![image-20200727195758246](/Users/bytedance/Library/Application Support/typora-user-images/image-20200727195758246.png)



## clip-path

### clip-path

- 关键实现： `clip-path`
- 具体分析：css3 新属性 `clip-path` 可以实现区域裁剪，现在浏览器支持较好的有三个函数：`clip-path: circle(50px at 50px 50px)` 以 `50px 50px` 的地方为圆心裁剪一个半径 50px 的圆；`clip-path: ellipse(30px 40px at 50px 50px)` 以 `50px 50px` 的地方为圆心裁剪一个横向半径 30px，纵向半径 40px 的椭圆；`clip-path: polygon(50% 0, 100% 50%, 50% 100%, 0 50%)` 按照多个坐标剪裁一个多边形，此处是菱形。有了 `clip-path`，就可以轻易的实现任意多边形了：[地址](http://js.jirengu.com/penut/1/edit?html,css,output)

![image-20200727200819188](/Users/bytedance/Library/Application Support/typora-user-images/image-20200727200819188.png)

## border-radius

### 自适应的椭圆

- 关键实现：`border-radius`
- 具体分析：`border-radius` 竟然可以设置 8 个角的半径～ 其中水平方向（对角线上下有弧度的地方）和垂直方向（对角线左右有弧度的地方）各四个，可以用 `/` 分割。如果水平或垂直方向指定的值少于四个，则会按照和 `margin、padding` 一样的规则重复。如果只指定来水平方向的，那么垂直方向的跟水平方向的一样。

![image-20200727202322589](/Users/bytedance/Library/Application Support/typora-user-images/image-20200727202322589.png)

## min-content

### 自适应宽度

- 关键实现：`min-content`关键字
- 具体分析：如何实现一个元素的宽度根据后代元素的最大固定元素宽度自适应呢？绞尽脑汁，也只能利用 `display: inline-block` 的包裹特性实现一个不完全的版本：[地址](http://js.jirengu.com/xifuw/1/edit?html,css,output) 这种方法的缺陷是文本脱离了文档流，高度未计入包含块。但是如果利用 `min-content` 关键字，可以一行代码实现且无副作用：[地址](http://js.jirengu.com/pepig/1/edit?html,css,output)

![image-20200727203557201](/Users/bytedance/Library/Application Support/typora-user-images/image-20200727203557201.png)

## box-shadow

### 单侧投影

- 关键实现：`box-shadow`
- 具体分析：`box-shadow` 前两个参数指定阴影的x、y偏移量，注意若为正数时整体向右/向下偏移，那么相应的左方/上方会空出一部分来（可以用来隐藏模糊半径或扩张半径），负数相反；第三个参数是阴影模糊半径，即高斯模糊多增加出来的过度颜色；第四个参数是阴影扩张半径，表示阴影增加的尺寸，可以是负数，表示阴影缩短的尺寸：[地址](http://js.jirengu.com/xahek/1/edit?html,css,output)

```
  box-shadow: 0 5px 4px -4px black;
```

第二个参数使阴影整体下移 5px ，第三个参数使阴影四周多了 4px 的高斯模糊（注意由于整体下移了 5px，所以此时上方还是没有阴影露出的），第四个参数又把阴影整体缩小了 4px，，所以左右两边才没有出现模糊半径导致的高斯模糊阴影色，从而实现单侧投影。

![image-20200727204951976](/Users/bytedance/Library/Application Support/typora-user-images/image-20200727204951976.png)

还可以逗号分隔设置多个阴影色，比如下面的两侧投影效果：[地址](http://js.jirengu.com/pavak/1/edit?html,output)

```
  box-shadow: 5px 0 5px -5px black,
             -5px 0 5px -5px black;
```

![image-20200727204843781](/Users/bytedance/Library/Application Support/typora-user-images/image-20200727204843781.png)

## drop-shadow

### 不规则投影

- 关键实现：`filter: drop-shadow()`
- 具体分析：`box-shadow` 不能透过透明背景显示出来，不能越过伪元素/子元素显示出来，而这些`drop-shadow`能做到。（但无论哪种投影都会被`clip-path`剪裁掉～～）[地址](http://js.jirengu.com/rosef/1/edit?html,css,output)

```
filter: drop-shadow(2px 2px 10px rgba(0,0,0,.5));
```

![image-20200727212441599](/Users/bytedance/Library/Application Support/typora-user-images/image-20200727212441599.png)

## filter

### 滤镜的染色和褪色效果

前端开发大都了解糊滤的高斯模镜效果是`filter: blur()`实现的，但是却很少使用滤镜的其他几个调色效果。`filter` 的值有`blur()`、`drop-shadow()`、`url()`、`brightness()`、`contrast()`、`grayscale()`、`hue-rotate()`、`invert()`、`opacity()`、`saturate()`、`sepia()`～～可以使用复合形式如：`filter: sepia(1) saturate(4)`等。下面是filter属性值大集合：[地址](http://js.jirengu.com/cogam/1/edit?html,css,output)

## user-select

user-select属性可以控制是否可选，有五个值，为关键词：

```
user-select: none;
user-select: auto;
user-select: text;
user-select: contain;
user-select: all;
```

`none`

元素及其子元素的文本不可选中。 

`auto`

`auto` 的具体取值取决于一系列条件，具体如下：

- 在 `::before` 和 `::after` 伪元素上，采用的属性值是 `none`
- 如果元素是可编辑元素，则采用的属性值是 `contain`
- 否则，如果此元素的父元素的 `user-select` 采用的属性值为 `all`，则该元素采用的属性值也为 `all`
- 否则，如果此元素的父元素的 `user-select` 采用的属性值为 `none`，则该元素采用的属性值也为 `none`
- 否则，采用的属性值为 `text`

`text`

用户可以选择文本。

`all`

在一个HTML编辑器中，当双击子元素或者上下文时，那么包含该子元素的最顶层元素也会被选中。

`contain`

允许在元素内选择；但是，选区将被限制在该元素的边界之内。

