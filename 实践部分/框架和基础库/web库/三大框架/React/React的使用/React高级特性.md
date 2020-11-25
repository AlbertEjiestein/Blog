# React高级特性

### 函数组件

+ 纯函数，输入props，输出JSX
+ 没有实例，没有生命周期，没有state 
+ 不能扩展其它方法



### 非受控组件

react：myref.current

vue：this.refs.myref



非受控组件会将数据存储到DOM中，有些场景只能使用非受控组件：

+ 文件上传 <input type=file ref="this.fileName">

  通过 this.fileName.current来获取文件信息

+ 某些富文本编辑器，需要传入DOM元素



优先使用受控组件，符合React设计原则-数据驱动视图



### Portals

+ 组件默认会按照既定层次嵌套渲染
+ 如何让组件渲染到父组件以外

```jsx
ReactDOM.createPortal(
	<div style={{position: fixed}}>portal</div>,
    document.body // 设置div元素渲染到body级别上
)
```



### context

+ 公共信息（语言，主题 ）

```js
// 创建context
const ThemeContext = React.createContext(defaultValue)

// 提供方
<ThemeContext.Provider value={this.state.theme}></ThemeContext.Provider>

// 消费方
// 对于class组件，两种方法：
ThemeContext.contextType = ThemeContext
// 或者设置类的静态属性
static contextType = ThemeContext
通过this.context获取值

// 对于函数组件
<ThemeContext.Consumer>
    {通过value获取值}
</ThemeContext.Consumer>
// 或者使用useContext
```



### 异步组件

+ import()
+ React.lazy
+ React.Suspense

```js
const ContextDemo = React.lazy(() => import('./ContextDemo'))

<React.Suspense fallback={<div>Loading...</div>}>
	<ContextDemo>
<React.Suspense>
```



### 性能优化（重要）

+ shouldComponentUpdate SCU
+ PureComponent，SCU中实现了浅比较
+ immutable



### shouldComponentUpdate SCU

```js
shouldComponentUpdate(nextProps, nextState){
    if(nextState.count !== this.state.count){
        return true
    }
    return false
}
```

React默认：父组件有更新，子组件无条件也更新，这就是为什么shouldComponentUpdate 默认返回true的原因。

**SCU优化一定要配合不可变值**，如果出现下面的情况，则永远返回false，因为通过push方法直接改变了list的值，nextState.list 永远等于 this.state.list

```js
shouldComponentUpdate(nextProps, nextState){
    if(nextState.list !== this.state.list){
        return true
    }
    return false
}

this.state.list.push('xxx')
```

对数组或者对象做深度比较

```js
shouldComponentUpdate(nextProps, nextState){
	// _.isEqual一次性递归到底    
    if(_.isEqual(nextProps.list, this.props.list)){
        return false
    }
    return true
}
```



### 纯组件

+ PureComponent也要求是不可变值

+ PureComponent，类组件的浅比较，其原理就是重新实现了shouldComponentUpdate方法，然后在进行前后state和prop的比较

  把React.Component换成React.PureComponent就行

+ memo，函数组件的浅比较

  ```js
  function MyComponent(props){
      
  }
  function areEqual(prevProps, nextProps){
      // prevProps和nextProps传入render的返回结果一直则返回true，否则返回false
  }
  React.memo(MyComponent, areEqual)
  ```

  或者使用recompose库的pure方法，原理是将函数组件转为加上PureComponent的class组件

  ```js
  const optimizedComponent = pure(MyComponent)
  ```

  

### immutable.js

+ 不可变值
+ 共享数据结构
+ 有一定的学习和迁移成本



### 高阶组件

+ mixin，已被React弃用

+ 高阶组件HOC，一种设计模式

  ```js
  // HOC的核心思想就是将传入的props全部透传给包裹的组件，类似vue中$props
  function HOCComponent(WrappedComponent){
      class Factory extends React.Component {
          constructor(){
              this.state = {
              // state 即多个组件的公共逻辑的数据
              }
          }
          // 修改state 不需要透传props
          render(){
              return (
                  <div>
                  	<WrappedComponent {...this.props} newProp={this.state} />
                  </div>
              )
          }
      }
      return Factory
  }
  
  
  const App = () => {
      return <div>{this.props}</div>
  }
  
  export default HOCComponent(App)
  ```

  redux提供的connect是高阶组件

  vue中的高阶组件是什么样子的？[参考](https://juejin.im/entry/5a524420f265da3e2e6252c5)

+ Render Props

  ```js
  // Render Props 的核心思想
  // 通过一个函数将 class组件的 state作为props传递给纯函数组件
  class Factory extends React.Component {
  	constructor(){
  		this.state = {
  		// state 即多个组件的公共逻辑的数据
  		}
  	}
  	// 修改state 不需要透传props
  	render(){
  		return <div>{this.props.render(this.state)}</div>
  	}
  }
  
  const App = () => {
      <Factory render={
          (props) => <p>{props}</p>
      } />
  }
  ```

+ HOC vs Render Props
  + HOC模式简单，但会增加组件层级，难维护
  + Render Props：代码简单，不过也不推荐使用，容器组件嵌套

























