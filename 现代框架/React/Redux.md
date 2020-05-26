```
// 维持应用的 state
// 提供 getState() 方法获取 state
// 提供 dispatch(action) 方法更新 state
// 通过 subscribe(listener) 注册监听器
// 通过 subscribe(listener) 返回的函数注销监听器
// createStore参数为可变参数，第一个参数为reducer，第二个参数为初始state
export function createStore(...arg){
    let state = null;
    let reducer = arg[0];
    // 使用第二个参数为state初始化
    if(arg.length > 1){state = arg[1]}
    // 保存监听器的数据
    let listeners = [];
    let getState = () => state;
    let subscribe = listener => listeners.push(listener)
    let dispatch = (action) =>{
        //执行reducer函数，更新状态
        state = reducer(state,action)
        //遍历listeners，执行之中的监听器函数
        listeners.forEach(listener => listener())
    }
    return {
        getState,
        dispatch,
        subscribe
    }
}
```