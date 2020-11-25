# javascript的执行过程

范围从大到小进行讲解：

脚本或模块 -> 事件循环 -> 宏任务和微任务 -> 函数 -> 语句

### 1. 事件循环

浏览器和node环境：JavaScript引擎等待宿主环境分配宏观任务



------

### 2. 宏观和微观任务

我们把宿主发起的任务称为宏观任务，把 JavaScript 引擎发起的任务称为微观任务。

每个宏观任务包含了一个微观任务队列。因此，**宏观任务的队列就相当于事件循环**。

setTimeout和Promise、async/await使用

异步执行的顺序:



------

### 3. 函数的执行

![image-20200719110455112](/Users/bytedance/Library/Application Support/typora-user-images/image-20200719110455112.png)

#### 3.1 闭包

闭包与普通函数的区别是，它携带了执行的环境，即函数体保存了对外部变量的引用。

《你不知道的javascript》中关于闭包的使用场景：

+ 定时相关：setTimeout、setInterval
+ 事件监听
+ 异步请求



#### 3.2 执行上下文组成

执行上下文，从名字上看，就是函数执行时，所需要的全部信息，因此也肯定包括了词法环境（或者叫作用域）。

下边是三个阶段的执行上下文：

执行上下文在 ES3 中，包含三个部分。

+ scope：作用域，也常常被叫做作用域链。
+ variable object：变量对象，用于存储变量的对象。
+ this value：this 值。

在 ES5 中，我们改进了命名方式，把执行上下文最初的三个部分改为下面这个样子。

+ lexical environment：词法环境，当获取变量时使用。
+ variable environment：变量环境，当声明变量时使用。
+ this value：this 值。

在 ES2018 中，执行上下文又变成了这个样子，this 值被归入 lexical environment，但是增加了不少内容。以这个版本为标准。

+ lexical environment：词法环境，当获取变量或者 this 值时使用。
+ variable environment：变量环境，当声明变量时使用。
+ code evaluation state：用于恢复代码执行位置。
+ Function：执行的任务是函数时使用，表示正在被执行的函数。
+ ScriptOrModule：执行的任务是脚本或者模块时使用，表示正在被执行的代码。
+ Realm：使用的基础库和内置对象实例。
+ Generator：仅生成器上下文有这个属性，表示当前生成器。



这里说一下变量对象，变量对象在javascript中是不可直接访问的，只有进入到执行上下文时，变量对象被激活成活动对象(activation object, AO)，AO的属性才可以被访问。

变量对象包含如下：

1. 函数的所有形参
2. 函数声明
3. 变量声明

看下边的例子：

```
function foo(a) {
  var b = 2;
  function c() {}
  var d = function() {};

  b = 3;
}

foo(1);
```

当进入执行上下文后，这时候的 AO 是：

```
AO = {
    arguments: {
        0: 1,
        length: 1
    },
    a: 1,
    b: undefined,
    c: reference to function c(){},
    d: undefined
}
```



#### 3.3 执行上下文切换机制

在执行上下文中，**this值**指向的就是函数执行时的上下文，那么一旦this值改变，整个语句的效果可能都会发生改变，这就引出了**函数调用切换上下文**的机制。

![image-20200719153256376](/Users/bytedance/Library/Application Support/typora-user-images/image-20200719153256376.png)

**函数家族**

在`javascript`中有8类函数：

普通函数、箭头函数、class中的方法、生成器函数、class本质也是函数、异步函数：异步普通函数、异步箭头、异步生成器



执行上下文的切换可以分函数内没有this和有this两种情况。

**this关键字的行为**

有一句很著名的话：**普通函数的 this 值由“调用它所使用的引用”决定**，这句话该怎么理解呢？其实不是大家理解的那么简单。看一个例子：

```
function showThis() {
	console.log(this);
}

const obj = {
	showThis: showThis
}

showThis(); // global
obj.showThis(); // obj
```

我们获取函数表达式，返回的并非是函数本身，而是一个**Reference类型**。该类型包含两部分：一个对象和一个属性值。

再看上边的例子，执行showThis()返回的Reference包含了global对象和showThis函数，执行obj.showThis()返回的是obj对象和showThis函数。

再看一个例子，是**使用箭头函数**的情况：

```

const showThis = () => {
    console.log(this);
}

var o = {
    showThis: showThis
}

showThis(); // global
o.showThis(); // global
```

如果现在再用 **this 值由“调用它所使用的引用”决定**这套规则显然是不成立了。因此从运行时的角度来看，this 跟面向对象毫无关联，它是与函数调用时使用的表达式相关。

最后看一个**class中方法**的例子：

```
class C {
    showThis() {
        console.log(this);
    }
}
var o = new C();
var showThis = o.showThis;

showThis(); // undefined
o.showThis(); // o
```

在方法中，我们看到 this 的行为也不太一样，它得到了 undefined 的结果。这是为什么？**因为class被设计成了按照严格模式执行的**



**this关键字的机制**

函数调用时能够记住上下文，必定有一个机制来保存这些信息。

在 JavaScript 标准中，为函数规定了用来保存定义时上下文的私有属性[[Environment]]。

当一个函数执行时，会创建一条新的执行环境记录，记录的外层词法环境（outer lexical environment）会被设置成函数的[[Environment]]。

先来看一个没有this的例子：

```
var a = 1;
foo();

在别处定义了foo：

var b = 2;
function foo(){
    console.log(b); // 2
    console.log(a); // error
}
```

foo函数的[[Environment]]属性保存了定义时的上下文，执行foo()函数时，会生成一条记录，记录的词法环境会被设置为保存的[[Environment]]属性。



而 this 则是一个更为复杂的机制，JavaScript 标准定义了 [[thisMode]] 私有属性。

[[thisMode]] 私有属性有三个取值。

+ lexical：表示从上下文中找 this，这对应了箭头函数。
+ global：表示当 this 为 undefined 时，取全局对象，对应了普通函数。
+ strict：当严格模式时使用，this 严格按照调用时传入的值，可能为 null 或者 undefined。

然后看一个有this的例子：

```
"use strict"
function showThis(){
    console.log(this);
}

var o = {
    showThis: showThis
}

showThis(); // undefined
o.showThis(); // o
```



**操作this的内置函数**

call、apply、bind



------

### 4. 语句

**Completion Record类型**

语句这部分涉及到一个类型：`Completion Record`，用于描述异常、跳出等语句执行过程。

`Completion Record` 表示一个语句执行完之后的结果，它有三个字段：

+ [[type]] 表示完成的类型，有 **break continue return throw 和 normal** 几种类型；
+ [[value]] 表示语句的返回值，如果语句没有，则是 empty，除了表达式语句，一般没啥用；
+ [[target]] 表示语句的目标，通常是一个 JavaScript 标签

因为javascript语句存在着嵌套关系，所以执行过程是在一个树形结构上执行的，树形结构的每一个节点执行后产生 Completion Record，根据语句的结构和 Completion Record，JavaScript 实现了各种分支和跳出逻辑。

**语句分类**

![img](https://static001.geekbang.org/resource/image/98/d5/98ce53be306344c018cddd6c083392d5.jpg)

**普通语句**

什么样的语句是普通语句？没有带控制能力的。

其 Completion Record的 [[type]] 为 normal，这里设置[[type]]有啥作用？JavaScript引擎会识别它，遇到normal就会执行下一句，遇到return就会跳出等。

**语句块**

语句块就是一条条普通语句组成的