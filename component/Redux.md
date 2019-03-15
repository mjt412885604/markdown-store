# redux复杂吗？

## redux本身的功能是什么
>在项目中，我们往往不会纯粹的使用redux，而是会配合其他的一些工具库提升效率，比如react-redux，让react应用使用redux更容易，类似的也有wepy-redux，提供给小程序框架wepy的工具库。

redux本身有哪些作用？我们先来快速的过一下redux的核心思想（工作流程）：

- 将状态统一放在一个`state`中，由`store`来管理这个`state`
- 这个`store`由`reducer`创建，`reducer`的作用是接受之前的状态，返回一个新的状态。
- 外部改变`state`的唯一方法是通过调用`store`的`dispatch`方法，触发一个`action`，这个`action`被对应的`reducer`处理，于是`state`完成更新。
- 可以通过`subscribe`在`store`上添加一个监听函数，`store`中`dispatch`方法被调用时，会执行这个监听函数。
- 可以添加中间件

在这个工作流程中，redux需要提供的功能是:

- 创建store，即：`createStore()`
- 将多个reducer合并为一个reducer，即：`combineReducers()`
- 创建出来的store提供`subscribe`，`dispatch`，`getState`这些方法。
- 应用中间件，即：`applyMiddleware()`

我们看下redux的源码目录：

![redux-源码](https://user-gold-cdn.xitu.io/2018/9/10/165c24bf9503182b?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

就是这么点，至于其他的如`compose`,`bindActionCreators`都是一些工具方法。下面我们就逐个来看看其源码实现

>你可以在github上克隆源码到本地，我后面的分析你可以参照着源码看。

### createStore的实现

函数的大致结构是这样:
```javascript
function createStore(reducer, preloadedState, enhancer) {
    if(enhancer){
        return enhancer(createStore)(reducer, preloadedState)
    } 
    
    let currentReducer = reducer // 当前store中的reducer
    let currentState = preloadedState // 当前store中存储的状态
    let currentListeners = [] // 当前store中放置的监听函数
    let nextListeners = currentListeners // 下一次dispatch时的监听函数
    // 注意：当我们新添加一个监听函数时，只会在下一次dispatch的时候生效。
    
    //...
    
    // 获取state
    function getState() {
        //...
    }
    
    // 添加一个监听函数，每当dispatch被调用的时候都会执行这个监听函数
    function subscribe() {
        //...
    }
    
    // 触发了一个action，因此我们调用reducer，得到的新的state，并且执行所有添加到store中的监听函数。
    function dispatch() {
        //...
    }
   
    //...
    
    //dispatch一个用于初始化的action，相当于调用一次reducer
    //然后将reducer中的子reducer的初始值也获取到
    //详见下面reducer的实现。
    
    
    return {
        dispatch,
        subscribe,
        getState,
        //下面两个是主要面向库开发者的方法，暂时先忽略
        //replaceReducer,
        //observable
    }
}
```

可以看出，`createStore`方法创建了一个`store`，但是并没有直接将这个`store`的状态`state`返回，而是返回了一系列方法，外部可以通过这些些方法（`getState`）获取`state`，或者间接地（通过调用`dispatch`）改变`state`。

至于state，被存在了闭包中。

我们再来详细的看看每个模块是如何实现的。

#### getState
```javascript
function getState() {
    return currentState
}
```
很简单，其实这很像面向对象编程中封装只读属性的方法，只提供数据的getter方法，而不直接提供setter

#### subscribe
```javascript
function subscribe(listener) {
    // 添加到监听函数数组，
    // 注意：我们添加到了下一次dispatch时才会生效的数组
    nextListeners.push(listener)
    
    let isSubscribe = true //设置一个标志，标志该监听器已经订阅了
    // 返回取消订阅的函数，即从数组中删除该监听函数
    return function unsubscribe() {
        if(!isSubscribe) {
            return // 如果已经取消订阅过了，直接返回
        }
        
        isSubscribe = false
        // 从下一轮的监听函数数组（用于下一次dispatch）中删除这个监听器。
        const index = nextListeners.indexOf(listener)
        nextListeners.splice(index, 1)
    }
}
```

#### dispatch
```javascript
function dispatch(action) {
    //调用reducer，得到新state
    currentState = currentReducer(currentState, action);
    
    //更新监听数组
    currentListener = nextListener;
    //调用监听数组中的所有监听函数
    for(let i = 0; i < currentListener.length; i++) {
        const listener = currentListener[i];
        listener();
    }
}
```

#### combineReducers
在理解`combineReducers`之前，我们先来想想reducer的功能：reducer接受一个旧的状态和一个action，当这个action被触发的时候，reducer处理后返回一个新状态。

也就是说 ，reducer负责状态的管理（或者说更新）。在实际使用中，我们应用的状态是可以分成很多个模块的，比如一个典型社交网站的状态可以分为：用户个人信息，好友列表，消息列表等模块。理论上，我们可以用一个reducer去处理所有状态的维护，但是这样做的话，我们一个reducer函数的逻辑就会太多，容易产生混乱。

因此我们可以将逻辑（reducer）也按照模块划分，每个模块再细分成各个子模块，开发完每个模块的逻辑后，再将reducer合并起来，这样我们的逻辑就能很清晰的组合起来。

对于我们的这种需求，redux提供了combineReducers方法，可以把子reducer合并成一个总的reducer。

来看看redux源码中combineReducers的主要逻辑：

```javascript
function combineReducers(reducers) {
    //先获取传入reducers对象的所有key
    const reducerKeys = Object.keys(reducers)
    const finalReducers = {} // 最后真正有效的reducer存在这里
    
    //下面从reducers中筛选出有效的reducer
    for(let i = 0; i < reducerKeys.length; i++){
        const key  = reducerKeys[i]
        
        if(typeof reducers[key] === 'function') {
            finalReducers[key] = reducers[key] 
        }
    }
    const finalReducerKeys = Object.keys(finalReducers);
    
    //这里assertReducerShape函数做的事情是：
    // 检查finalReducer中的reducer接受一个初始action或一个未知的action时，是否依旧能够返回有效的值。
    let shapeAssertionError
  	try {
    	assertReducerShape(finalReducers)
  	} catch (e) {
    	shapeAssertionError = e
  	}
    
    //返回合并后的reducer
    return function combination(state= {}, action){
  		//这里的逻辑是：
    	//取得每个子reducer对应的state，与action一起作为参数给每个子reducer执行。
    	let hasChanged = false //标志state是否有变化
        let nextState = {}
        for(let i = 0; i < finalReducerKeys.length; i++) {
            //得到本次循环的子reducer
            const key = finalReducerKeys[i]
            const reducer = finalReducers[key]
            //得到该子reducer对应的旧状态
            const previousStateForKey = state[key]
            //调用子reducer得到新状态
            const nextStateForKey = reducer(previousStateForKey, action)
            //存到nextState中（总的状态）
            nextState[key] = nextStateForKey
            //到这里时有一个问题:
            //就是如果子reducer不能处理该action，那么会返回previousStateForKey
            //也就是旧状态，当所有状态都没改变时，我们直接返回之前的state就可以了。
            hasChanged = hasChanged || previousStateForKey !== nextStateForKey
        }
        return hasChanged ? nextState : state
    }
} 
```

### 为什么需要中间件 `applyMiddleware`
在redux的设计思想中，reducer应该是一个纯函数
>在程序设计中，若一个函数符合以下要求，则它可能被认为是纯函数：
>- 此函数在相同的输入值时，需产生相同的输出。函数的输出和输入值以外的其他隐藏信息或状态无关，也和由I/O设备产生的外部输出无关。
>- 该函数不能有语义上可观察的函数副作用，诸如“触发事件”，使输出设备输出，或更改输出值以外物件的内容等。
>
>纯函数的输出可以不用和所有的输入值有关，甚至可以和所有的输入值都无关。但纯函数的输出不能和输入值以外的任何资讯有关。纯函数可以传回多个输出值，但上述的原则需针对所有输出值都要成立。若引数是传引用调用，若有对参数物件的更改，就会影响函数以外物件的内容，因此就不是纯函数。

总结一下，纯函数的重点在于:
- 相同的输入产生相同的输出（不能在内部使用Math.random,Date.now这些方法影响输出）
- 输出不能和输入值以外的任何东西有关（不能调用API获得其他数据）
- 函数内部不能影响函数外部的任何东西（不能直接改变传入的引用变量），即不会突变

reducer为什么要求使用纯函数，文档里也有提到，总结下来有这几点：

- state是根据reducer创建出来的，所以reducer是和state紧密相关的，对于state，我们有时候需要有一些需求（比如打印每一次更新前后的state，或者回到某一次更新前的state）这就对reducer有一些要求。
- 纯函数更易于调试
    - 比如我们调试时希望action和对应的新旧state能够被打印出来，如果新state是在旧state上修改的，即使用同一个引用，那么就不能打印出新旧两种状态了。
    - 如果函数的输出具有随机性，或者依赖外部的任何东西，都会让我们调试时很难定位问题。
- 如果不使用纯函数，那么在比较新旧状态对应的两个对象时，我们就不得不深比较了，深比较是非常浪费性能的。相反的，如果对于所有可能被修改的对象（比如reducer被调用了一次，传入的state就可能被改变），我们都新建一个对象并赋值，两个对象有不同的地址。那么浅比较就可以了。

至此，我们已经知道了，`reducer`是一个纯函数，那么如果我们在应用中确实需要处理一些副作用（`比如异步处理`，`调用API等操作`），那么该怎么办呢？这就是中间件解决的问题。下面我们就来讲讲redux中的中间件

### 中间件处理副作用的机制
中间件在redux中位于什么位置，我们可以通过这两张图来看一下。

先来看看不用中间件时的redux工作流程:

![中间件在redux中位于什么位置，我们可以通过这两张图来看一下。](https://user-gold-cdn.xitu.io/2018/9/10/165c24bf8ff7f1aa?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

1. dispatch一个action
2. 这个action被reducer处理
3. reducer根据action更新store（中的state）

而用了中间件之后的工作流程是这样的：

![而用了中间件之后的工作流程是这样的：](https://user-gold-cdn.xitu.io/2018/9/10/165c24bf96cbce24?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

1. dispatch一个action
2. 这个action先被中间件处理（比如在这里发送一个异步请求）
3. 中间件处理结束后，再发送一个action（有可能是原来的action，也可能是不同的action，视中间件功能而不同）
4. 中间件发出的action可能继续被另一个中间件处理，进行类似3的步骤。即中间件可以链式串联。
5. 最后一个中间件处理完后，dispatch一个符合reducer处理标准的action
6. 这个标准的action被reducer处理
7. reducer根据action更新store（中的state）

那么中间件该如何融合到redux中呢?

在上面的流程中，2-4的步骤是关于中间件的，但凡我们想要添加一个中间件，我们就需要写一套2-4的逻辑。如果每个中间件我们手动串联的话，就不够灵活，增删改以及调整顺序，都需要修改中间件串联的逻辑。

所以redux提供了一种解决方案，将中间件的串联操作进行了封装，经过封装后，上面的步骤2-5就可以成为一个整体，如下图：

![11](https://user-gold-cdn.xitu.io/2018/9/10/165c24bf9ca1fbe5?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

我们只需要改造store自带的dispatch方法，action发生后，先给中间件处理，最后再dispatch一个action交给reducer去改变状态。

#### 中间件在redux的实现
还记得redux 的createStore()方法的第三个参数enhancer吗：

```javascript
function createStore(reducer, preloadedState, enhancer) {
    if(enhancer是有效的){  
        return enhancer(createStore)(reducer, preloadedState)
    } 
    
    //...
}
```

在这里，我们可以看到，`enhancer`（可以叫做强化器）是一个函数，这个函数接受一个常规`createStore`函数’作为参数，返回一个加强后的`createStore`函数。

这个加强的过程中做的事情，其实就是改造dispatch，添加上中间件。`redux`提供的`applyMiddleware()`方法返回的就是一个`enhancer`。

`applyMiddleware`，顾名思义，应用中间件，输入为若干中间件，输出为`enhancer`。下面就来看看这个方法的源码：

```javascript
function applyMiddleware(...middlewares) {
    // 返回一个函数A，函数A的参数是一个createStore函数。
    // 函数A的返回值是函数B，其实也就是一个加强后的createStore函数，大括号内的是函数B的函数体
    return createStore => (...args) => {
        //用参数传进来的createStore创建一个store
        const store  = createStore(...args)
        //注意，我们在这里需要改造的只是store的dispatch方法
        
        let dispatch = () => {  //一个临时的dispatch
            					//作用是在dispatch改造完成前调用dispatch只会打印错误信息
            throw new Error(`一些错误信息`)
        } 
        //接下来我们准备将每个中间件与我们的state关联起来（通过传入getState方法），得到改造函数。
        const middlewareAPI = {
            getState: store.getState,
            dispatch: (...args) => dispatch(...args)
        }
        //middlewares是一个中间件函数数组，中间件函数的返回值是一个改造dispatch的函数
        //调用数组中的每个中间件函数，得到所有的改造函数
        const chain = middlewares.map(middleware => middleware(middlewareAPI))
        
        //将这些改造函数compose（翻译：构成，整理成）成一个函数
        //用compose后的函数去改造store的dispatch
        dispatch = compose(...chain)(store.dispatch)
        // compose方法的作用是，例如这样调用：
        // compose(func1,func2,func3)
        // 返回一个函数: (...args) => func1( func2( func3(...args) ) )
        // 即传入的dispatch被func3改造后得到一个新的dispatch，新的dispatch继续被func2改造...
        
        // 返回store，用改造后的dispatch方法替换store中的dispatch
        return {
            ...store,
            dispatch
        }
    }
}
```

总结一下，applyMiddleware的作用是：

    1. 从middleware中获取改造函数
    2. 把所有改造函数compose成一个改造函数
    3. 改造dispatch方法

## 总结
至此，redux的核心源码已经讲完了，最后不得不感叹，redux写的真的美，真tm的简洁。

redux的核心功能还是创建一个store来管理state。通过reducer的层级划分，可以得到一棵有多层的state树。