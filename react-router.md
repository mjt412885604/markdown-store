# [react-router v4](https://reacttraining.com/react-router/web/guides/philosophy)
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

### 匹配路径
React Router使用 path-to-regexp 包来判断路径的 path prop 是否匹配当前路径，它将 path 字符串转换成正则表达式与当前的路径进行匹配，关于 path 字符串更多的可选格式，可以查阅 path-to-regexp [文档](https://github.com/pillarjs/path-to-regexp)。

当路由与路径匹配的时候，一个具有以下属性的 match 对象将会被作为 prop 传入

- `url` - 当前路径与路由相匹配的部分
- `path` - 路由的`path`
- `isExact` - `path` === `pathname`
- `params` - 一个包含着 `pathname` 被 `path-to-regexp` 捕获的对象

```
**Note: **目前，路由的路径必须是绝对路径 
```

### 创建我们自己的路由
`<Route>` 可以在`router`中的任意位置被创建，不过一般来说将他们放到同一个地方渲染更加合理，你可以使用 `<Switch>` 组件来组合 `<Route>`，`<Switch>`将遍历它的 `children` 元素（路由），然后只匹配第一个符合的 `pathname`

>例如：
1. `/` - 主页
2. `/roster` - 队伍名单
3. `/roster/:number` - 队员的资料，使用球员的球衣号码来区分
4. `/schedule` - 队伍的赛程表

为了匹配路径，我们需要创建带 path prop的 <Route> 元素

```javascript
<Switch>
  <Route exact path='/' component={Home}/>
  {/* both /roster and /roster/:number begin with /roster */}
  <Route path='/roster' component={Roster}/>
  <Route path='/schedule' component={Schedule}/>
</Switch>
```

### `<Route>` 将会渲染什么
`Route` 可以接受三种 prop 来决定路径匹配时渲染的元素，只能给 `<Route>` 元素提供一种来定义要渲染的内容。

1. `<component>` - 一个 React 组件，当一个带有 component prop 的路由匹配的时候，路由将会返回 prop 提供的 `component` 类型的组件（通过 `React.createElement` 渲染）。
2. `render` - 一个返回 React 元素的方法，与 `component` 类似，也是当路径匹配的时候会被调用。写成内联形式渲染和传递参数的时候非常方便。
3. `children` - 一个返回 React 元素的方法。与前两种不同的是，这种方法总是会被渲染，无论路由与当前的路径是否匹配。

```javascript
<Route path='/page' component={Page} />
const extraProps = { color: 'red' }
<Route path='/page' render={(props) => (
  <Page {...props} data={extraProps}/>
)}/>
<Route path='/page' children={(props) => (
  props.match
    ? <Page {...props}/>
    : <EmptyPage {...props}/>
)}/>
```

一般来说，我们一般使用 `component` 或者 `render，children` 的使用场景不多，而且一般来说当路由不匹配的时候最好不要渲染任何东西。在我们的例子中，不需要向路由传递任何参数，所有我们使用 `<component>`。

由 `<Route>` 渲染的元素将会带有一系列的 `props`，有 `match` 对象，当前的 `location` 对象，还有 `history` 对象（由 router 创建。

### 路由的嵌套
队员资料页的路由 `/roster/:number` 是在 `<Roster>` 组件而没有包含在 `<Switch>` 中。但是，只要 `pathname` 由 `/roster` 开头，它就会被 `<Roster>` 组件渲染。

在 `<Roster>` 组件中我们将渲染两种路径：
1. `/roster` - 只有当路径完全匹配 `/roster` 时会被渲染，我们要对该路径指定 `exact` 参数。
2. `/roster/:number` - 这个路由使用一个路径参数来捕获 `/roster` 后面带的 `pathname` 的部分。

```javascript
const Roster = () => (
  <Switch>
    <Route exact path='/roster' component={FullRoster}/>
    <Route path='/roster/:number' component={Player}/>
  </Switch>
)
```

将带有相同前缀的路由放在同一个组件中很方便，这样可以简化父组件并且让我们可以让我们在一个地方渲染所有带有相同前缀的组件。

举个例子，`<Roster>` 可以为所有以 `/roster` 开头的路由渲染一个标题

```javascript
const Roster = () => (
  <div>
    <h2>This is a roster page!</h2>
    <Switch>
      <Route exact path='/roster' component={FullRoster}/>
      <Route path='/roster/:number' component={Player}/>
    </Switch>
  </div>
)
```

### Path 参数
有的时候我们想捕捉 `pathname` 中的多个参数，举例来说，在我们的球员资料路由中，我们可以通过向路由的 `path` 添加路径参数来捕获球员的号码。

`:number` 部分代表在`pathname`中 `/roster/` 后面的内容将会被储存在 `match.params.number`。举例来说，一个为 `/roster/6` 的 `pathname` 将会生成一个如下的`params` 对象。

```javascript
{ number: '6' } // note that the captured value is a string
```

关于 path 参数可以查阅 path-to-regexp [文档](https://github.com/pillarjs/path-to-regexp)。

### Link
最后，我们的网站需要在页面之间导航，如果我们使用 `<a>` 标签导航的话，将会载入一整个新的页面。`React Router` 提供了一个 `<Link>` 组件来避免这种情况，当点击 `<Link>` 时，URL 将会更新，页面也会在不载入整个新页面的情况下渲染内容。

```javscript
import { Link } from 'react-router-dom'
const Header = () => (
  <header>
    <nav>
      <ul>
        <li><Link to='/'>Home</Link></li>
        <li><Link to='/roster'>Roster</Link></li>
        <li><Link to='/schedule'>Schedule</Link></li>
      </ul>
    </nav>
  </header>
)
```

`<Link> `使用 `to` `prop` 来决定导航的目标，可以是一个字符串，或者是一个 `location` 对象（包含 `pathname`, `search`, `hash` 和 `state` 属性）。当只是一个字符串的时候，将会被转化为一个 `location` 对象

```javascript
<Link to={{ pathname: '/roster/7' }}>Player #7</Link>
```
**Note: **目前，链接的 pathname 必须是绝对路径。

### Redirect 重定向
Redirect接受一个 `to` 参数，类似于 `Link` 参数，这个组件直接执行并重定向到指定页面。

> 例如

```javascript
<Redirect
  to={{
    pathname: "/login",
    state: { from: props.location }
  }}
/>
```

可以结合React高阶函数进行应用的登录验证，例如：
```javascript
const fakeAuth = {
  isAuthenticated: localstorage.getItem('token')
}

const PrivateRoute = ({ component: Component, ...rest }) => (
  <Route
    {...rest}
    render={props =>
      fakeAuth.isAuthenticated ? (
        <Component {...props} />
      ) : (
        <Redirect
          to={{
            pathname: "/login",
            state: { from: props.location }
          }}
        />
      )
    }
  />
)
```

### No Match (404)
当 `pathname` 匹配不到路由时候正确的做当应该是只想一个404页面。

Route路由组件规定没有 `path` 时会指向404组件，例如：

```javascript
<Route component={NoMatch} /> // 404
```

### NavLink导航式路由
如果当前 `pathname` 和 `NavLink` 跳转匹配的话就会添加设定的样式，例如：
```javascript
import { NavLink } from 'react-router-dom'
<NavLinkNavLin 
  to="/faq"
  activeClassName="selected"
>FAQs</NavLink>

// or style

<NavLink
  to="/faq"
  activeStyle={{
    fontWeight: 'bold',
    color: 'red'
   }}
>FAQs</NavLink>

// exact
<NavLink
  exact
  to="/profile"
>Profile</NavLink>

// only consider an event active if its event id is an odd number
const oddEvent = (match, location) => {
  if (!match) {
    return false
  }
  const eventID = parseInt(match.params.eventID)
  return !isNaN(eventID) && eventID % 2 === 1
}

<NavLink
  to="/events/123"
  isActive={oddEvent}
>Event 123</NavLink>
```

## withRouter
`react-router` 提供了一个`withRouter`组件 
`withRouter`可以包装任何自定义组件，将`react-router` 的 `history`,`location`,`match` 三个对象传入。 
无需一级级传递`react-router` 的属性，当需要用的`router` 属性的时候，将组件包一层`withRouter`，就可以拿到需要的路由信息.

```javascript
import {withRouter} from 'react-router-dom' ;
var Test = withRouter(({history,location,match})=>{ 
    return <div>{location.pathname}</div>
})
```

正常情况下 只有`Router` 的`component`组件能够自动带有三个属性 如下的Home 组件有

```javascript
<Route exact path="/Home" component={Home}/>
var Home = ( {history,location,match})=>   <div>{location.pathname}</div>
```

如果使用了`react-router-redux`,则可以直接从 `state` 中的`router`属性中获取`location`。不需要再使用 `withRouter` 获取路由信息了


















