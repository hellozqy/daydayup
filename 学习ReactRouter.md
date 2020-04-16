# 学习React Router

### 1. 安装

通过npm安装

```
$ npm install --save react-router
```

### 2. 路由配置

函数组件方式

```jsx
React.render((
  <Router>
    <Route path="/" component={App}>
      <IndexRoute component={Dashboard} />
      <Route path="about" component={About} />
      <Route path="inbox" component={Inbox}>
        {/* 使用 /messages/:id 替换 messages/:id */}
        <Route path="/messages/:id" component={Message} />
        {/* 跳转/inbox/message/：id到/messages/：id用以兼容相对路径的跳转 */}
        <Redirect from="messages/:id" to="/messages/:id" />
      </Route>
    </Route>
  </Router>
), document.body)
```

其中的/messages/：id为绝对路径，绝对路径可能在动态路由中无法使用，当URL为/messages/：id时渲染APP-->Inbox-->Message组件，可以表现为更加简洁的URL。

index和about为相对路径，：id是路由传参

class组件方式

```jsx
class App extends Component {
	render (){
		return (
      <Router path='/' commponent = {App} >
      	<IndexRoute component = {Dashboard} />
      	<Route path='about' component={About} />
        <Route path='inbox' component={Index} >
        	<Route path='/messages/:id' component={Message} />
          <Redirect from="messages/:id" to="/messages/:id" />
        </Route>
       </Router>
    )
	}
}
```

### 3. onEnter和onLeave

在Route中可以定义onEnter和onLeave这两个hook，他们可以用于权限验证或者是在路由跳转前保存数据。

在路由跳转过程中，onLeave会在所有将离开的路由中触发，有下至上，onEnter会在所有将进入的路由中触发，从上至下，例如当从/messages/5跳转到/about时执行顺序：

- `/messages/:id` 的 `onLeave`
- `/inbox` 的 `onLeave`
- `/about` 的 `onEnter`

### 4.路径语法

1. ：paramName -匹配一段位于` /`、`？`、`#`之后的URL。被命中的部分将被作为一个参数
2. `()` -在它内部的内容被认为是可选的
3. `*` -匹配任意字符（非贪婪匹配）直到命中下一个字符或者整个URL的末尾，并创建一个`splat`参数
4. 相对路径时完整路径为它的所有祖先节点的路径和自身指定的相对路径拼接而成
5. 绝对路径可以忽略匹配的嵌套关系，直接以自身路径作为完整路径
6. 匹配顺序为自上而下

### 5.History

常用的history有三种形式

可以从React Router中引入它们：

~~~jsx
// JavaScript 模块导入（译者注：ES6 形式）
import { browserHistory } from 'react-router'
// 然后传递给<Router>
render(
  <Router history={browserHistory} routes={routes} />,
  document.getElementById('app')
)
~~~



- browserHistory：Browser history 是使用 React Router 的应用推荐的 history。它使用浏览器中的 [History](https://developer.mozilla.org/en-US/docs/Web/API/History) API 用于处理 URL，创建一个像`example.com/some/path`这样真实的 URL 。

- hashHistory：Hash history 使用 URL 中的 hash（`#`）部分去创建形如 `example.com/#/some/path` 的路由。

- createMemoryHistory：Memory history 不会在地址栏被操作或读取。这就解释了我们是如何实现服务器渲染的。同时它也非常适合测试和其他的渲染环境（像 React Native ）。和另外两种history的一点不同是你必须创建它，这种方式便于测试。

- ```jsx
  const history = createMemoryHistory(location)
  ```

### 6. IndexRoute与IndexLink



