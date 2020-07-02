# Redux

单项数据流原理

```js
// 初始化的state
const appState = {
  title: {
    text: 'React.js 小书',
    color: 'red',
  },
  content: {
    text: 'React.js 小书内容',
    color: 'blue'
  }
}

/* 
renderApp负责具体的渲染state，观察者模式中监听到state改变，便会调用renderApp进行更新渲染
renderTitle和renderContent负责详细的title和content渲染
 */
function renderApp(newState, oldState={}){
  if(newState === oldState) return
  console.log('render app...')
  renderTitle(newState.title, oldState.title);
  renderContent(newState.content, oldState.content);
}


function renderTitle(newTitle, oldTitle={}){
  if(newTitle === oldTitle) return
  console.log('render title...')
  let titleDOM = document.getElementById('title');
  titleDOM.innerHTML = newTitle.text;
  titleDOM.style.color = newTitle.color;
}

function renderContent(newContent, oldContent={}){
  if(newContent === oldContent) return
  console.log('render content...')
  let contentDOM = document.getElementById('content');
  contentDOM.innerHTML = newContent.text;
  contentDOM.style.color = newContent.color;
}

/*
changeState既充当了获取初始化数据的功能，也充当了生成更新数据的功能。
是一个纯函数，接收两个参数：state和action，返回更改后的state。
注意：不能修改传入的state，只能浅拷贝再进行修改
*/
function changeState(state, action){
  if(!state){
    return appState
  }
  switch(action.type){
    case 'UPDATE_TITLE_TEXT':
      return {
        ...state,
        title: {
          ...state.title,
          text: action.text
        }
      }
    case 'UPDATE_TITLE_COLOR':
      return {
        ...state,
        title: {
          ...state.title,
          color: action.color
        }
      }
    default:
      return state
  }
}

/* 
创建store统一管理，对外暴露三个方法，分别负责获取state，修改state，监听state变化后渲染state
 */
function createStore(reducer){
  let state = null;
  const getState = () => state;
  const listeners = [];
  const subscribe = (listener) => listeners.push(listener);
  const dispatch = (action) => {
    state = reducer(state, action);
    listeners.forEach(listener => listener())
  }
  dispatch({}) // 初始化state
  return {
    getState,
    dispatch,
    subscribe
  }
}

// 1. 创建store实例
const store = new createStore(changeState);

// 2. 通过设置观察者模式，监听state变化并渲染，设置新旧state对比进行优化
let oldState = store.getState();
store.subscribe(() => {
  const newState = store.getState();
  renderApp(newState, oldState);
  oldState = newState;
})

// 3. 首次渲染
renderApp(store.getState())

// 4. 通过dispatch来修改state
store.dispatch({ type: 'UPDATE_TITLE_TEXT', text: '《React.js 小书》' }) // 修改标题文本
store.dispatch({ type: 'UPDATE_TITLE_COLOR', color: 'blue' }) // 修改标题颜色

```

