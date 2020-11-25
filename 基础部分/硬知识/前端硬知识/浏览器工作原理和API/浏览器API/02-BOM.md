# JavaScript BOM对象－BOM介绍

![BOM－浏览器对象模型](https://itbilu.com/img/demo/bom.png)

#### 2.2 `DOM`（`document`）相关对象

`DOM`可以认为是`BOM`的一个子集，`DOM`中文档操作相关对象，如：[`Node`](http://itbilu.com/javascript/js/Ny3B0ddWg.html)、[`Document`](http://itbilu.com/javascript/js/N1PfJNgMe.html)、[`Element`](http://itbilu.com/javascript/js/VJYOZrWml.html)等`DOM`节点类型对象，都是做为`window`对象的子属性出现的。

`document`是`window`对象的一个属性，它是一个`Document`对象实例，表示当前窗口中文档对象。通过该对象，可以对文档和文档中元素、节点等进行操作。

2.2.1 `Document`

有两种方式可以让浏览器扩展与系统剪贴板交互: [`Document.execCommand()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/execCommand) 方法以及现代的异步的 [Clipboard API](https://developer.mozilla.org/en-US/docs/Web/API/Clipboard_API).

[`Document.execCommand()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/execCommand) 方法常通过以下指令使用:

- `document.execCommand("copy")`
- `document.execCommand("cut")`
- `document.execCommand("paste")`

Clipboard API 提供了异步的连接来直接读写剪贴板内容. 例, 如此从剪贴板读取文本:

```js
navigator.clipboard.readText().then(text => outputElem.innerText = text);
```

2.2.2 **`HTMLInputElement`**

**`HTMLInputElement.select()`** 方法选中一个 [``](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/textarea) 元素或者一个带有 text 字段的 [``](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/input) 元素里的所有内容。



https://itbilu.com/javascript/js/4k9JcnZRl.html