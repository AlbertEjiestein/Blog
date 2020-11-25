# CSS语言：普通规则之选择器

## 伪元素的使用

伪元素的语法跟伪类相似，但是实际产生的效果却是把不存在的元素硬选出来。

目前兼容性达到可用的伪元素有以下几种。

+ ::first-line
+ ::first-letter
+ ::before
+ ::after（在CSS2中是:after，建议都适用具有伪元素特征的::）

### 扩大可点击区域

- 关键实现：伪元素
- 具体分析：利用伪元素和定位达到鼠标移到边缘时候出现手型且可点击

```
.expand-range {
  position: relative;
}
.expand-range:after {
  content: '';
  position: absolute;
  top: -10px; right: -10px; bottom: -10px; left: -10px;
  cursor: pointer;
}
```

推荐使用`scss`，对`scss`不熟悉的参考阮一峰老师博客[SASS用法指南](https://www.ruanyifeng.com/blog/2012/06/sass.html)。

```
@mixin expand-range($top: -10px, $right: $top, $bottom: $top, $left: $right, $position: relative) {
    position: $position;
    &:after {
      content: '';
      position: absolute;
      top: $top;
      right: $right;
      bottom: $bottom;
      left: $left;
      cursor: pointer;
    }
  }

.test { @include expand-range($top: -5px, $position: absolute) }
```

### 巧用层叠上下文

- 关键实现： 伪元素 层叠上下文
- 具体分析： 利用层叠上下文和 `z-index: -1` 特性实现伪元素覆盖背景同时又不会遮挡文字（具体实现原理参考[这里](https://juejin.im/post/5ce607a7e51d454f6f16eb3d#heading-28) ）。这是一个非常常用又好用的技巧，善加利用可以达到很多意想不到的效果。[地址](http://js.jirengu.com/nozih/2/edit?html,css,output)

![image-20200727184653421](/Users/bytedance/Library/Application Support/typora-user-images/image-20200727184653421.png)

### 边框内圆角

- 关键实现：伪元素 层叠上下文
- 具体分析：利用伪元素实现圆角矩形并叠加在父元素的背景之上文字之下：[地址](http://js.jirengu.com/dikux/1/edit)

![image-20200727195758246](/Users/bytedance/Library/Application Support/typora-user-images/image-20200727195758246.png)

