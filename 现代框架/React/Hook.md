
Hooks

# 基本思想

在 React 16 之前，只有类组件才具有生命周期，并且能够使用 state 功能。

类组件的不足：

* 使用 HOC 实现复用。会带来层级冗余。
* 使用分散的生命周期分别注册和卸载事件。会带来维护成本。
* 父组件向子组件传递函数时，必须绑定 this。

因此在 React 16 中引入 hook。

https://react.docschina.org/docs/hooks-intro.html

# 常见的 hook

## useState

```
const [state, setState] = useState(initialState);
setCount(count + 1); //更新指定值
setCount(c => c + 1); //告诉函数如何更新，不依赖具体变量
```

## useMemo（实现 shouldComponentUpdate）

```
const data = useMemo(()=>({number}),[number]);
```

## useCallback

```
const addClick = useCallback(()=>{
	setNumber(number+1);
},[number]);
```

## useReducer

```

const initialState = 0;

function reducer(state, action) {

switch (action.type) {

case 'increment':

return {number: state.number + 1};

case 'decrement':

return {number: state.number - 1};

default:

throw new Error();

}

}

function init(initialState){

return {number:initialState};

}

function Counter(){

const [state, dispatch] = useReducer(reducer, initialState,init);

return (

<>

Count: {state.number}

<button onClick={() => dispatch({type: 'increment'})}>+</button>

<button onClick={() => dispatch({type: 'decrement'})}>-</button>

</>

)

}

```

## useContext

```

function SubCounter(){

const {state, dispatch} = useContext(CounterContext);

return (

<>

<p>{state.number}</p>

<button onClick={()=>dispatch({type:'ADD'})}>+</button>

</>

)

}

/* class SubCounter extends React.Component{

static contextTypes = CounterContext

this.context = {state, dispatch}

} */

function Counter(){

const [state, dispatch] = useReducer((reducer), initialState, ()=>({number:initialState}));

return (

<CounterContext.Provider value={{state, dispatch}}>

<SubCounter/>

</CounterContext.Provider>

)

}

```

## useEffect（componentDidMount，componentDidUpdate，componentWillUnmount）

```

function FriendStatusWithCounter(props) {

const [count, setCount] = useState(0);

useEffect(() => {

document.title = `You clicked ${count} times`;

});

const [isOnline, setIsOnline] = useState(null);

useEffect(() => {

function handleStatusChange(status) {

setIsOnline(status.isOnline);

}

ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);

return () => {

ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);

};

});

// ...

}

```

## useRef

用于获取函数组件当前的实例，用于传递给子组件，获取实例当前 props 等。

```

function Parent() {

let [number, setNumber] = useState(0);

return (

<>

<Child />

<button onClick={() => setNumber({ number: number + 1 })}>+</button>

</>

)

}

let input;

function Child() {

const inputRef = useRef();

console.log('input===inputRef', input === inputRef);

input = inputRef;

function getFocus() {

inputRef.current.focus();

}

return (

<>

<input type="text" ref={inputRef} />

<button onClick={getFocus}>获得焦点</button>

</>

)

}

```

```
function Example() {
  const [count, setCount] = useState(0);
  const latestCount = useRef(count);

  useEffect(() => {
    // Set the mutable latest value
    latestCount.current = count;
    setTimeout(() => {
      // Read the mutable latest value
      console.log(`You clicked ${latestCount.current} times`);
    }, 3000);
  });
  // ...
  ```
  

# 实现原理

先导问题：函数组件没有实例，那 state 是存在哪里的？如何确保每次重新渲染都能拿到正确的值？状态改变后为什么可以触发重新渲染？

React 的 hook 是基于 fiber 实现的。

Fiber 节点中有一个 memoizedState 属性，用链表方式维护函数内部的 hook。因此对于 hook 的顺序必须不能发生变化。


usememo原理


# 模拟生命周期

## 模拟整个生命周期中只运行一次的方法

```

useMemo(() => {

// execute only once

}, []);

```

## 模拟 shouldComponentUpdate

```
const areEqual = (prevProps, nextProps) => {
// 返回结果和 shouldComponentUpdate 正好相反
// 访问不了 state
};
React.memo(Foo, areEqual);
```

## 模拟 componentDidMount

```

useEffect(() => {

// 这里在 mount 时执行一次

}, []);

```

## 模拟 componentDidUpdate

```

const mounted = useRef();

useEffect(() => {

if (!mounted.current) {

mounted.current = true;

} else {

// 这里只在 update 是执行

}

});

```

## 模拟 componentDidUnmount

```

useEffect(() => {

// 这里在 mount 时执行一次

return () => {

// 这里在 unmount 时执行一次

}

}, []);

```

## props 到 state

使用 useEffect，传入依赖项。可以实现需求，但无法获取到 prevProps。

```js

useEffect(() => {

console.log('initialCount changed', props.initialCount);

}, [props.initialCount])

```

可以使用 ref 保存实例的 prevProps 值。

```

import React, { useState, useRef, useEffect } from 'react';

function Counter({ initialCount }) {

const [count, setCount] = useState(initialCount)

const prevCountRef = useRef(initialCount);

useEffect(() => {

prevCountRef.current = initialCount;

})

if (initialCount !== prevCountRef.current && count !== initialCount) {

setCount(initialCount)

}

return <div>

<p>counter2:{count}</p>

<div>

<button onClick={() => { setCount(count + 1) }}>+</button>

</div >

</div >

}

function App() {

const [count, setCount] = useState(0)

return (

<div className="App">

<h3>hook ver</h3>

<p>counter1:{count}</p>

<div>

<button onClick={() => { setCount(count + 1) }}>+</button>

</div>

<Counter initialCount={count}></Counter>

</div>

);

}

export default App;

```

# 原理实现

https://www.netlify.com/blog/2019/03/11/deep-dive-how-do-react-hooks-really-work/

# Not Magic, just Arrays

```
const MyReact = (function() {
  let hooks = [],
    currentHook = 0 // array of hooks, and an iterator!
  return {
    render(Component) {
      const Comp = Component() // run effects
      Comp.render()
      currentHook = 0 // reset for next render
      return Comp
    },
    useEffect(callback, depArray) {
      const hasNoDeps = !depArray
      const deps = hooks[currentHook] // type: array | undefined
      const hasChangedDeps = deps ? !depArray.every((el, i) => el === deps[i]) : true
      if (hasNoDeps || hasChangedDeps) {
        callback()
        hooks[currentHook] = depArray
      }
      currentHook++ // done with this hook
    },
    useState(initialValue) {
      hooks[currentHook] = hooks[currentHook] || initialValue // type: any
      const setStateHookIndex = currentHook // for setState's closure!
      const setState = newState => (hooks[setStateHookIndex] = newState)
      return [hooks[currentHook++], setState]
    }
  }
})()
```

# 常见问题

1. hook 是如何把 useState 和内部的 state 对应上的？

依赖不变的调用顺序。因此不能在条件判断、函数嵌套内部使用 hook。应该将条件判断放在 hook 内部实现。

2. 可以只在更新时候运行 effect 吗？

使用 ref 存储一个布尔值变量表示是否是首次渲染。并在 effect 中检查该标识。

3. componentwillreceiveprops 的替代方案？

官方：使用 getDerivedStateFromProps 替代。会带来新的误区（内部更新无效，需要把上一个 prop 值存入 state）。

推荐升级策略：

* 类组件：使用完全受控组件。使用 key 标识的完全不受控组件。
* 使用 hook


一个很牛逼很多功能的class组件，里面有业务生命周期（获取数据前后、弹出窗口前后...），怎么在函数组件里面直接复用它【举例】
先注入到函数组件的props，再到useeffect关键节点里面执行业务生命周期

# 完整 demo

```
import React, { Fragment, useState, useEffect } from 'react';
import axios from 'axios';
 
const useDataApi = (initialUrl, initialData) => {
  const [data, setData] = useState(initialData);
  const [url, setUrl] = useState(initialUrl);
  const [isLoading, setIsLoading] = useState(false);
  const [isError, setIsError] = useState(false);
 
  useEffect(() => {
    const fetchData = async () => {
      setIsError(false);
      setIsLoading(true);
 
      try {
        const result = await axios(url);
 
        setData(result.data);
      } catch (error) {
        setIsError(true);
      }
 
      setIsLoading(false);
    };
 
    fetchData();
  }, [url]);
 
  return [{ data, isLoading, isError }, setUrl];
};
 
function App() {
  const [query, setQuery] = useState('redux');
  const [{ data, isLoading, isError }, doFetch] = useDataApi(
    'https://hn.algolia.com/api/v1/search?query=redux',
    { hits: [] },
  );
 
  return (
    <Fragment>
      <form
        onSubmit={event => {
          doFetch(
            `http://hn.algolia.com/api/v1/search?query=${query}`,
          );
 
          event.preventDefault();
        }}
      >
        <input
          type="text"
          value={query}
          onChange={event => setQuery(event.target.value)}
        />
        <button type="submit">Search</button>
      </form>
 
      {isError && <div>Something went wrong ...</div>}
 
      {isLoading ? (
        <div>Loading ...</div>
      ) : (
        <ul>
          {data.hits.map(item => (
            <li key={item.objectID}>
              <a href={item.url}>{item.title}</a>
            </li>
          ))}
        </ul>
      )}
    </Fragment>
  );
}
 
export default App;
```

---

深度好文：https://overreacted.io/zh-hans/a-complete-guide-to-useeffect/