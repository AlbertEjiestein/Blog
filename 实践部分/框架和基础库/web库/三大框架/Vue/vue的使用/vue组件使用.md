## vue组件使用

### 组件间通信

老生常谈，vue或react组件间通信无非如下几种情况：

#### 父传子

+ props
+ refs
+ children（不保证子组件的顺序）

#### 子传父

+ 直接使用$emit触发事件

  ```vue
  // 子组件 MyButton
  this.$emit('myEvent', value);
  // 父组件
  <MyButton @myEvent="triggerEvent"></MyButton>
  ```

+ 通过$parent，子组件告诉父组件该如何操作

#### 兄弟通信

$parent，其实也是通过$emit和$on，只不过把通信任务交给父组件来完成

#### 父代和子孙代

通过provide和inject：

```js
// 父代
export default {
	provide: {
        foo: "foo"
    }
    // 或者通过函数形式传值，类似data
    /* 
    provide() {
		return {
            foo: this
        }
    } 
    */
}

// 子孙代
export default {
    inject: ['foo']
}
```

#### 任意组件之间传值

+ vuex

+ 自定义事件或者事件总线，其实本质是观察者模式

```js
event = new Vue()
// 一个组件中
event.$emit('add', title)
// 另一个组件中
event.$on('add',this.addTitle)
// 这里使用addTitle而不是箭头函数是防止内存泄漏，addTitle可以在beforeDestroy中销毁 
event.$off('add',this.addTitle)
```



### 组件生命周期（重要）

单个组件的生命周期  官网

有父子组件的生命周期，假设有父组件Index和子组件List

created   mounted顺序 index created->list created->list mounted->index mounted

beforeUpdate  updated顺序 index beforeUpdate -> list beforeUpdate  ->list updated -> index updated

beforeDestory destoryed顺序  index beforeDestory -> list beforeDestory ->list destoryed-> index destoryed