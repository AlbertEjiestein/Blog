### JSX语法

react和vue对比：

|          |                            React                             |          Vue          |
| :------: | :----------------------------------------------------------: | :-------------------: |
| 组件数据 |                            state                             |         data          |
|   插值   |                     {message} 一个大括号                     |      {{message}}      |
|  表达式  |                     {flag? true: false}                      |  {flag? true: false}  |
|   图像   |                <img src={ths.state.imgUrl} />                |  <img :src=imgUrl />  |
|  class   |                          className                           |         class         |
| 条件判断 |                           if else                            | v-if v-else-if v-else |
| 渲染列表 |                             map                              |         v-for         |
|   事件   |               onClick\|修改事件this指向\|传参                |        @click         |
|   表单   | 受控组件：将数据存到state中；非受控：真实数据储存在 DOM 节点中 |    v-model双向绑定    |
| 父子通信 |                  props(数据\|回调-状态提升)                  |     props  $emit      |
| 修改数据 |                           setState                           |       $nextTick       |
| 生命周期 |                       挂载->更新->卸载                       |   挂载->更新->卸载    |



**JSX基本使用**

组件数据  插值   表达式   图像  class和style



**条件**



**列表**



**事件**

+ react的event不是原生的event，是**合成事件**(SyntheticEvent)

+ event是SyntheticEvent，event.nativeEvent是原生事件，所有的事件都被挂载到document上，和DOM事件不一样
+ event是原生的，事件被挂载到当前元素，和DOM事件一样



**表单**

表单可以分受控和非受控组件两种来实现：

+ 受控：通过对input|textarea设置value &onChange实现双向绑定，模仿了Vue的v-model原理

+ 非受控：通过ref绑定来实现



**react中父子通信**

通过设置props为数据或者为回调函数，实现组件的父传子或者子传父



**setState使用：**

+ 不能直接修改state，不可变值（函数式编程，纯函数），所以不能对数组使用push pop splice，可以使用concat，ES6的扩展运算符，slice，filter

+ setState可能是异步的，

  + 直接使用是异步的，类似于vue的$nextTick

  ```js
  this.setState({
      count: this.state.count + 1
  }, () => {
  	console.log('count is ', this.state.count )
  }) 	
  ```

  + 在setTimeOut和自定义DOM事件中，setState是同步的，估计是因为这两个本身都是异步的原因

  ```js
  // 定时器
  setTimeOut(() => {
      this.setState({
          count: this.state.count + 1
      })
  })
  
  --------------------------------
  // dom事件
  bodyClickHandler = () => {
      this.setState({
          count: this.state.count + 1
      })
      console.log('count is ', this.state.count )
  }
  componentDidMount(){
      document.body.addEventListener('click', this.bodyClickHandler)
  }
  componentWillUnmount(){
      document.body.removeEventListener('click', this.bodyClickHandler)
  }
  ```

+ 可能会被合并，对象会被合并，函数不会被合并

  ```js
  // 假设count初始值为0
  
  this.setState({
      count: this.state.count + 1
  })
  this.setState({
      count: this.state.count + 1
  })
  this.setState({
      count: this.state.count + 1
  })
  
  console.log(count) // 1
  ----------------------------------
  
  this.setState((preState, props) => {
      return {
          count: preState.count + 1
      }
  })
  this.setState((preState, props) => {
      return {
          count: preState.count + 1
      }
  })
  this.setState((preState, props) => {
      return {
          count: preState.count + 1
      }
  })
  console.log(count) // 3
  ```

  



**生命周期**

对比react和vue的生命周期，单个组件和父子组件的生命周期