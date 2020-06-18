# vue基本使用

### 插值和指令

v-html会有xss风险，会覆盖子元素

### computed和watch

computed有缓存

watch默认浅监听，对于引用类型，要想深度监听，设置deep:true

### class和style

动态属性

### 条件渲染

v-if v-else的用法

v-if和v-show的区别

### 循环渲染

v-for遍历数组和对象

key不能乱写（random和index）

v-for和v-if不能一起使用

### 事件

 event参数是原生Event，自定义参数

观察事件被绑定到哪里？

​	vue的事件是被挂载到当前元素

事件修饰符，按键修饰符@click.ctrl

### 表单

v-model 

常见表单项 textarea checkbox radio select

表单修饰符 lazy number trim