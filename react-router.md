# react-router v4
React Router V4 相较于前面三个版本有根本性变化，首先是遵循Just Component的 API 设计理念，其次API方面也精简了不少，对新手来说降低了学习难度，React Router V4 遵循了 React 的理念：万物皆组件。因此 升级之后的 Route、Link、Switch等都是一个普通的组件。

React Router V4 基于 Lerna 管理多个 Repository。在此代码库包括：
>- react-router React Router 核心
>- react-router-dom 用于 DOM 绑定的 React Router
>- react-router-native 用于 React Native 的 React Router
>- react-router-redux React Router 和 Redux 的集成
>- react-router-config 静态路由配置帮助助手

## 插件引入
通常我们在 React 的使用中，一般要引入两个包，`react`和 `react-dom`，那么`react-router`和`react-router-dom`是不是两个都要引用呢？他们两个只要引用一个就行了，不同之处就是后者比前者多出了`<Link>` `<BrowserRouter>`这样的 `DOM` 类组件。因此我们只需引用`react-router-dom`这个包就OK了。当然，如果搭配redux，你还可以使用`react-router-redux`。

## Router
当开始一个新项目时，你应该决定要使用哪种 router。对于在浏览器中运行的项目，我们可以选择 `<BrowserRouter>` 和 `<HashRouter>` 组件，`<BrowserRouter>` 应该用在服务器处理动态请求的项目中（知道如何处理任意的URI），`<HashRouter>` 用来处理静态页面（只能响应请求已知文件的请求）。

通常来说更推荐使用 `<BrowserRouter>`，可是如果服务器只处理静态页面的请求，那么使用 `<HashRouter>` 也是一个足够的解决方案。

对于我们的项目，我们假设所有的页面都是由服务器动态生成的，所以我们的 router 组件选择 `<BrowserRouter>`。

## History
每个 `router` 都会创建一个 `history` 对象，用来保持对当前位置[1]的追踪还有在页面发生变化的时候重新渲染页面。`React Router` 提供的其他组件依赖在 context 上储存的 history 对象，所以他们必须在 router 对象的内部渲染。一个没有 router 祖先元素的 React Router 对象将无法正常工作.

## 渲染一个 `<Router>`
`Router` 的组件只能接受一个子元素，为了遵照这种限制，创建一个 `<App>` 组件来渲染其他的应用将非常方便.

```javascript
import { BrowserRouter } from 'react-router-dom'
ReactDOM.render((
  <BrowserRouter>
    <App />
  </BrowserRouter>
), document.getElementById('root'))
```

## Route
`<Route>` 组件是 `React Router` 的主要组成部分，如果你想要在路径符合的时候在任何地方渲染什么东西，你就应该创造一个 `<Route>` 元素

### Path
一个 `<Route>` 组件需要一个 `string` 类型的 `path prop` 来指定路由需要匹配的路径。举例来说，`<Route path='/roster/' />` 将匹配以 /roster  开始的路径，当当前的路径和 `path` 匹配时，`route` 将会渲染对应的 `React` 元素。当路径不匹配的时候 ，路由不会渲染任何元素。

```javascript
<Route path='/roster'/>
// when the pathname is '/', the path does not match
// when the pathname is '/roster' or '/roster/2', the path matches
// If you only want to match '/roster', then you need to use
// the "exact" prop. The following will match '/roster', but not
// '/roster/2'.
<Route exact path='/roster'/>
// You might find yourself adding the exact prop to most routes.
// In the future (i.e. v5), the exac t prop will likely be true by
// default. For more information on that, you can check out this
```

**Note: **在匹配路由的时候，React Router 只会关心相对路径的部分，所以如下的 URL

```javascript
http://pddocotr.com/test/one
```
React Router 只会尝试匹配 `/test/one`。