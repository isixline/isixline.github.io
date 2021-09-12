---
title: React Router 
date: 2021-08-24 00:00 
tags:
    - react
---

## react-router 和 react-router-dom

- react-router：提供了router的核心api。如Router、Route、Switch等
- react-router-dom：基于react-router，并提供了BrowserRouter、HashRouter，Link等组件，可以通过dom操作触发事件控制路由

ps：react-router-dom里包含了react-router的依赖，无需重复引入

## 一个例子

```javascript
 <Router>
    <Route path="/" component={App}>
        <Route path="about" component={About}/>
        <Route path="inbox" component={Inbox}>
            <Route path="messages/:id" component={Message}/>
        </Route>
    </Route>
</Router>
```

ps：当path以"/"开头，为绝对路径，否则为相对路径(如：/inbox/message/:id)

## Router

当渲染组件时，Router 会自动向 Route 组件中注入一些有用的信息，尤其是路径中动态部分的参数，如：id。

```javascript
    const id = this.props.params.id;
```

## IndexRoute
默认路由
```javascript
<IndexRoute component={Home}/>
```

## Redirect
从跳转 /inbox/messages/:id 到 /messages/:id 
```javascript
<Redirect from="messages/:id" to="/messages/:id"/>
```

## hook：onEnter onLeave

## 页面跳转
组件方式
```javascript
 <Link to="/about">关于我们</Link>
```

被Route包裹的组件，代码内调用
```javascript
this.props.history.push("/about")
```

## 路由参数获取
被Route包裹的组件
```javascript
this.props.location
```

