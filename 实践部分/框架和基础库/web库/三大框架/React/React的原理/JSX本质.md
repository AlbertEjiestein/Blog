### JSX本质

和vue的编译类似

React.createElement('div', attrs, [child1,child2, child3])

React.createElement('div', attrs, child1,child2, child3)

React.createElement(List, attrs, child1,child2, child3)

// h函数

// 返回vnode

// patch

// 第一个参数可能是组件，也可以是html tag，如果是组件会继续往下解析