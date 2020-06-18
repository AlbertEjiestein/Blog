# vue组件使用

### props和$emit

父子组件通信

### 组件间通讯-自定义事件

父子通信：props和$emit

兄弟通信：自定义事件或者事件总线，其实本质是vue的实例，vue实例本身就实现了$emit和$on

比如

event = new Vue()

一个组件中，event.$emit('add', title)

另一个组件中，event.$on('add',this.addTitle)

这里使用addTitle而不是箭头函数是防止内存泄漏，addTitle可以在beforeDestroy中销毁 

event.$off('add',this.addTitle)

### 组件生命周期（重要）

 单个组件的生命周期  官网

有父子组件的生命周期，假设有父组件Index和子组件List

created   mounted顺序 index created->list created->list mounted->index mounted

beforeUpdate  updated顺序 index beforeUpdate -> list beforeUpdate  ->list updated -> index updated

beforeDestory destoryed顺序  index beforeDestory -> list beforeDestory ->list destoryed-> index destoryed