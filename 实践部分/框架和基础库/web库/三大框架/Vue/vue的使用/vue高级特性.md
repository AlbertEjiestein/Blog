## Vue高级特性

### 自定义v-model

适合颜色，选择器这种

https://www.cnblogs.com/wind-lanyan/p/7899428.html，

**$attrs的使用**

v-bind = "$attrs"

inheritAttrs：避免顶层容器继承属性



### $nextTick和refs

vue是异步渲染

data改变之后，DOM不会立刻渲染

$nextTick会在DOM渲染之后触发，以获取DOM节点



### 插槽

**匿名插槽**

```vue
<slot></slot>
```



**具名插槽**

```html
// 子组件 KInput
<div>
    <slot name="content"></slot>
</div>

// 父组件 需要使用template标签
<KInput>
  <template v-slot:content>coderyao</template>
</KInput>
```



**作用域插槽**

有时候父组件的插槽内容需要访问子组件中才有的数据，这个时候直接访问是访问不到的。在子组件中通过`v-bind`指令绑定父组件想要访问的数据，然后父组件通过`v-slot:default="对象"`访问子组件中的数据。

```vue
// 子组件 KInput
<div>
    <slot name="content" :username="username"></slot>
  </div>
// 父组件
// slotProps是一个包含所有插槽 prop 的对象，支持解构
<KInput>
    <template v-slot:content="{username}">username: {{username}}</template>
</KInput>
```



### 动态、异步组件(很少人知道)

v-bind:is = "component-name"

使用keep-alive来保持这些组件的状态，以避免反复重渲染导致的性能问题。

<keep-alive>  <component v-bind:is="currentTabComponent"></component> </keep-alive>



异步加载：import()

### keep-alive

缓存，切换频繁，不需要重复渲染

vue常见性能优化

### mixin

Vue3 composition API

缺点：

信息来源不明确，不利于阅读

多mixin可能会造成命名冲突

mixin和组件可能出现多对多的关系，复杂度较高