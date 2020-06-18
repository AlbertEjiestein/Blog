# Vue高级特性

### 自定义v-model

适合颜色，选择器这种

https://www.cnblogs.com/wind-lanyan/p/7899428.html，

### $nextTick和refs

vue是异步渲染

data改变之后，DOM不会立刻渲染

$nextTick会在DOM渲染之后触发，以获取DOM节点

### slot

作用域插槽

<span>

  <slot v-bind:user="user">

​    {{ user.lastName }}

  </slot>

</span>

slotProps是一个包含所有插槽 prop 的对象

<current-user>

  <template v-slot:default="slotProps">

​    {{ slotProps.user.firstName }}

  </template>

</current-user>

具名插槽

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