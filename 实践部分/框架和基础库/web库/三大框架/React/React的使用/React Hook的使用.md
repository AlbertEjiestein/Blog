# React Hook的使用

## 全局状态管理

### 配置store

分成两部分：globalStore和recordStore

**globalStore**存储一些公共信息，如下：

```
const state = {
 userInfo: null,
 channelInfo: {
  channelList: [],
  current: null,
 },
 deviceInfo: {
  deviceList: [],
  current: null,
 },
};
```

**recordStore**存储一些record相关的信息，如下：（**对getRecordStartTs和getRecordCursor作用不了解**）

```
const state = {
 records: [],
 recordMap: {},
 expandedKeys: [],
 currentRecord: null,
 recordContextMenuProp: { visible: false },
 treeValue: '',
 started: false,
 startTs: getRecordStartTs(),
 cursor: getRecordCursor(),
 currentDeviceId: null,
 treeData: [],
 treeSearchValue: '',
 isClearing: false,
 latency: 0,
 mapSetRecord: null,
 mapSetVisible: false
};
```

最后对两个store合成为一个store，类似于redux的createStore函数，用于创建根store。

> 注意useReducer和useState的区别：

> useState用于管理每个组件的state，因此范围仅在函数组件内，类组件中是通过this.setState实现的；

> useReducer用于管理一些复杂的state，不仅用于组件内，还可用于全局，传入reducer，返回对应的state和dispatch方法，通过dispatch可以修改对应store的state，和redux的dispatch一个原理。

```
export function useContextStore() {
 const [globalState, globalDispatch] = useReducer(GlobalReducer, GlobalState);
 const [recordState, recordDispatch] = useReducer(RecordReducer, RecordState);
 return {
  globalStore: {
   state: globalState,
   dispatch: globalDispatch,
  },
  recordStore: {
   state: recordState,
   dispatch: recordDispatch,
  },
 };
}
```

### 配置reducer

我们知道store是一个树形结构，每一个store分支用于管理对应的state，那么每一个store分支就对应一个reducer，只不过最后把所有分支合并成了一个，然后通过context往下传递给子组件.

- globalStore对应的reducer

```
const reducer = (state, action) => {
 switch (action.type) {
  case 'SET_FIELDS':
   return { ...state, ...action.payload };
  default:
   throw new Error(`unknown action.type [${action.type}]`);
 }
};
```

- recordStore对应的reducer

```
const reducer = (state, action) => {
 switch (action.type) {
  case 'SET_FIELDS':
   return { ...state, ...action.payload };
  case 'CLEAR_RECORDS':
   return {
    ...state,
    records: [],
    treeData: [],
    currentRecord: null,
    isClearing: true,
   };
  default:
   throw new Error(`unknown action.type [${action.type}]`);
 }
};
```

### 配置context

```
const Context = React.createContext(null);
const store = useContextStore();
<Context.Provider value={store}>
 {children}
</Context.Provider>
```

这样所有的子组件都可以修改并使用全局的state了，但是这里没有使用Context.Consumer而是使用useContext以扁平化的形式获取到context数据，这是因为如果有多个祖先组件使用多个Context.Provider传值，而子组件用到了多个Provider的值，那么通过Context.Consumer会造成嵌套，而useContext则不会。

使用Consumer示例如下：

```
// Theme context，默认的 theme 是 “light” 值
const ThemeContext = React.createContext('light');

// 用户登录 context
const UserContext = React.createContext({
  name: 'Guest',
});

class App extends React.Component {
  render() {
    const {signedInUser, theme} = this.props;

    // 提供初始 context 值的 App 组件
    return (
      <ThemeContext.Provider value={theme}>
        <UserContext.Provider value={signedInUser}>
          <Layout />
        </UserContext.Provider>
      </ThemeContext.Provider>
    );
  }
}

function Layout() {
  return (
    <div>
      <Sidebar />
      <Content />
    </div>
  );
}

// 一个组件可能会消费多个 context
function Content() {
  return (
    <ThemeContext.Consumer>
      {theme => (
        <UserContext.Consumer>
          {user => (
            <ProfilePage user={user} theme={theme} />
          )}
        </UserContext.Consumer>
      )}
    </ThemeContext.Consumer>
  );
}
```

## React hook的使用

### 为何选择React Hooks用来状态逻辑复用

状态逻辑的复用主要有三种：render props、higher-order components和React Hooks，其中前两种容易生成回调地狱，只不过高阶组件由于装饰器的简洁性，看起来没有render props那么可怕，但组件嵌套造成的难以调试还是不能避免的。

hook的好处：

1. 多个状态不会产生嵌套，写法还是平铺的

1. 允许函数组件使用 state(useState) 和部分生命周期(useEffect)

1. 更容易将组件的 UI 与状态分离

### Custom Hooks

现在我们知道了hook可以在**不引入其他组件**的情况下复用组件之间的状态逻辑，这使得UI和状态分离，

如何自定义hook？

- 官网提供的例子：

```
import { useState, useEffect } from 'react';

function useFriendStatus(friendID) {  
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }

    ChatAPI.subscribeToFriendStatus(friendID, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(friendID, handleStatusChange);
    };
  });

  return isOnline;
}
```

- 任意门项目中的例子：

```
function useStore() {
  const store = useContext(Context);
  if (!store) {
    return new Error('Use Provider!');
  }
  return store;
}
```

```
// 定时器hook，学到了！
function useInterval(callback, delay) {
  const savedCallback = useRef();
  // 保存新回调
  useEffect(() => {
    savedCallback.current = callback;
  });
  // 建立 interval
  useEffect(() => {
    function tick() {
      savedCallback.current();
    }
    if (delay !== null) {
      const id = setInterval(tick, delay);
      return () => clearInterval(id);
    }
  }, [delay]);
}
```

```
function useStateWithCallback(initialState, callback) {
  const [state, setState] = useState(initialState);
  useEffect(() => callback(state), [state, callback]);
  return [state, setState];
}
```

### useMemo

这里最好结合类组件中的高级组件的使用来对比学习，mapStoreWithMemo是一个高阶组件，比如redux的connect，执行之后返回的是一个函数组件（class组件中返回的同样是一个类组件），比如官网提供的高阶组件示例：

```
// 此函数接收一个组件...
function withSubscription(WrappedComponent, selectData) {
  // ...并返回另一个组件...
  return class extends React.Component {
    
    // ...省略中间逻辑

    render() {
      // ... 并使用新数据渲染被包装的组件!
      // 请注意，我们可能还会传递其他属性
      return <WrappedComponent data={this.state.data} {...this.props} />;
    }
  };
}
```

下面标黑的部分等同于上面返回的组件，只不过这里又使用了**React.useMemo**来进行优化，**React.useMemo**返回一个[**memoized**](https://en.wikipedia.org/wiki/Memoization)**值，而不是**[**memoized**](https://en.wikipedia.org/wiki/Memoization)**函数**，也就是说下边这个return了传入的组件，这样就说通了。

> 补充1：**React.useCallback**返回一个[**memoized**](https://en.wikipedia.org/wiki/Memoization)**函数，而不是**[memoized](https://en.wikipedia.org/wiki/Memoization)值

> 补充2：对比**React.useMemo和**[**React.memo**](https://zh-hans.reactjs.org/docs/react-api.html#reactmemo)**，**前者可以指定依赖，后者则是直接根据传入的props来提高性能

```
const mapStoreWithMemo = <T, F>(Component: React.FC<T>, hook?: () => F): React.FC<Omit<T, 'store'>> => (props: T) => {
  const state = hook && hook() || {};
  return React.useMemo(() => (
    <Component {...props} store={state} />
  ), [...Object.values(props), ...Object.values(state)]);
};
```

mapStoreWithMemo的第二个参数是一个hook，返回的是需要的state数据

```
const RequestCollect = props => {
    ...
}

export default mapStoreWithMemo(RequestCollect, () => {
  const { globalStore } = useStore();
  const {
    state: { deviceInfo, channelInfo },
  } = globalStore;
  return {
    deviceInfo,
    channelInfo,
  };
});
```

### useCallback

- 和useMemo类似，只不过 useCallback 是用来缓存函数。

- 可以指定依赖项

### useEffect

- componentDidMount、componentDidUpdate 和 componentWillUnmount

- 和useMemo和useCallback都可以指定依赖项

- 不过useEffect没有返回值，而是会执行回调函数

### useState & useRef & useReducer

- 过时状态

useState返回了一个数组，分别是当前值和修改这个值的方法（类似类组件state和setState），注意的是每次执行这个方法都会**重新渲染该组件。**如果需要根据之前的状态更新状态，可以使用回调函数setState(preState => newState)，否则直接使用setState(newState)来修改数据，这个不太安全，如下：

```
function DelayedCount() {
  const [count, setCount] = useState(0);

  const handleClickAsync = () => {
    setTimeout(function delay() {
      setCount(count + 1);
    }, 3000);
  }

  return (
    <div>
      {count}
      <button onClick={handleClickAsync}>Increase async</button>
    </div>
  );
}
```



- 在3s内多次点击按钮，count最终的值只会加1，这是delay函数是一个闭包，它从初始渲染（使用0初始化时）中捕获了过时的count变量。目前有两者解决方法：

- **一个是使用回掉函数：**

```
function DelayedCount() {
  const [count, setCount] = useState(0);

  const handleClickAsync = () => {
    setTimeout(function delay() {
      setCount(count => count + 1);
    }, 3000);
  }

  return (
    <div>
      {count}
      <button onClick={handleClickAsync}>Increase async</button>
    </div>
  );
}
```

React 确保将最新状态值作为参数提供给更新状态函数setCount，这样过时闭包的问题解决了。

**另一个方法是使用useRef**：

```
function DelayedCount() {
  const [count, setCount] = useState(0);
  const ref = useRef(0);

  const handleClickAsync = () => {
    setTimeout(function delay() {
      setCount(ref.current += 1);
    }, 3000);
  }

  return (
    <div>
      {count}
      <button onClick={handleClickAsync}>Increase async</button>
    </div>
  );
}
```

- 复杂状态管理

使用useState用于管理简单的状态比较方便，当需要管理复杂的状态时，应该使用useReducerhook

### useRef & createRef & 回调ref & ref字符串

- useRef的使用

- - useRef只能用于函数组件

- - useRef返回一个可变的ref对象，该对象在组件的整个生命周期内保持不变，其current属性被初始化为传入的值(initialValue)

- - 修改current的值，不会引发组件重新渲染

- createRef

- - createRef只能用于类组件

- - createRef也有一个current属性

- - 组件每次重新渲染，createRef都会发生改变

- 回调ref

- - 只能用于类组件

- - ref属性的值是一个回调函数，这个函数接受一个组件实例或者DOM节点作为参数，以使它们能在其他地方被存储和访问

- - React 将在组件挂载时，会调用 ref 回调函数并传入 DOM 元素，当卸载时调用它并传入 null

- ref字符串

- - 正逐渐被官方废弃

## 网络请求

### SWR基本特性

SWR（stale-while-revalidate），实现的一个 React Hooks 版本的数据请求库，相比常见的数据请求库有很多新的特性，使用如下：

- tab页切换时，被focus的tab会自动获取数据，使用焦点自动在页面之间同步登录状态。

- 组件销毁然后再重新渲染时，优先使用本地缓存数据，一旦从缓存加载数据，SWR 会自动重新获取原始的数据，更新本地的缓存，常见的场景是点击一个链接推出之后再次进入，会使用缓存数据

- 定时重新获取数据，这在多个标签页、用户和设备上保持数据之间的同步很重要

- SWR 提供了一个 mutate 方法来直接修改请求返回的数据，这用于设置临时的局部状态，然后发起请求进行revalidate更新

- 在列表页中浏览器回退可以自动记忆滚动条位置，场景如加载更多时，点击进入某一项，退出时还是之前的位置

- SWR 默认用原生的 fetch 做请求，并假设使用 REST 风格的 API 进行调用，但是你也可以指定其他的任何异步请求库作为第二个参数，比如axios和graphql

```
import { request } from 'graphql-request'
import useSWR from 'swr'
const API = 'https://api.graph.cool/simple/v1/movies'
function Profile () {
 const { data, error } = useSWR(
  `{
   Movie(title: "Inception") {
    releaseDate
    actors {
     name
    }
   }
  }`,
  query => request(API, query)
 )
 if (error) return <div>failed to load</div>
 if (!data) return <div>loading...</div>
 return <div>Movie: {data.title}
```

- SWR 允许获取依赖于其他数据的数据，它可以确保最大程度的并行性，比如projects依赖user的id，如果港式user没获取到，会返回undefined显示loading...

```
import useSWR from 'swr'
function MyProjects () {
 const { data: user } = useSWR('/api/user')
 const { data: projects } = useSWR(
  () => '/api/projects?uid=' + user.id
 )
 if (!projects) return 'loading...'
 return 'You have ' + projects.length + ' projects'
}
```

- 将 SWR Hooks 与 React Suspense 一起使用，只需 SWR 的配置中启用 suspense: true

```
import { Suspense } from 'react'
import useSWR from 'swr'
function Profile () {
 const { data } = useSWR(
  '/api/user',
  fetch,
  { suspense: true }
 )
 return <div>hello, {data.name}</div>
}
function App () {
 return (
  <Suspense fallback={<div>loading...</div>}>
   <Profile/>
  </Suspense>
 )
}
```

### 项目中使用SWR

项目中使用了axios作为网络请求库，对get post put delete常见的四种http请求方法做了封装

```
const methods = ['get', 'post', 'put', 'delete'];
const http = {};
methods.forEach(v => {
 http[v] = (url, data, baseUrl?) => {
  if (!url) {
   return;
  }
  const axiosConfig: Config = {
   baseURL: baseUrl || DEFAULTCONFIG.baseURL,
   method: v,
   url,
   headers: {
    // 'x-csrf-token': getCookie('csrfToken')
   },
  };
  const instance = axios.create(DEFAULTCONFIG);
  // 请求拦截
  instance.interceptors.request.use(
   ...
  );
  // 返回拦截
  instance.interceptors.response.use(
   ...
  );
  if (v === 'get') {
   axiosConfig.params = data;
  } else if (v === 'post') {
   axiosConfig.data = data;
  } else if (data instanceof FormData) {
   axiosConfig.data = data;
  } else {
   axiosConfig.data = qs.stringify(data);
  }
  axiosConfig.startTime = new Date();
  return instance
   .request(axiosConfig)
   .then(res => res)
   .catch(err => {
    message.close(toastId);
    toastId = message.error(err.msg || '未知错误');
    return Promise.reject(err);
   });
 };
});
```

自定义请求方法为customFetch，然后外部只需传入url、method和body或者params即可获取请求的结果。

```
async function customFetch(url: string, { method, body }) {
 return http[method.toLowerCase()](url, body);
}
```

外部使用SWR来进行数据请求，MockGetDevice为对应接口的reducer

```
const { data: mockEnable, error: mockEnableErr } = useSWR(
  ['proxyServiceClient.MockGetDevice'],
  () =>
   proxyServiceClient.MockGetDevice({
    aid: currentChannel.app_id,
    did: currentDevice.device_id,
   }),
  SWR_DEFAULT_CONFIG
 );
```

这个reducer便是之前封装好的customFetch

```
MockGetDevice(req, option) {
  const query = queryStringify(req);
  const uri = `${this.uriPrefix}/anywhere/mock/getdevice/${query}`;
  const headers = { "Content-Type": "application/json" };
  return customFetch(uri, { method: "GET", headers }, option);
 }
```

### SWR原理

https://zhuanlan.zhihu.com/p/93824106

