---
title: Client-Side Routing
date: 2023-12-18 15:25:23
tags:
---



参考：[Chapter 17 Client-Side Routing](https://info340.github.io/client-side-routing.html)

# Client-Side Routing

这里讨论如何使用React来开发单页应用（Single Page Applications，SPA）。单页应用只有一个HTML文件，但使用DOM操作和AJAX请求来产生多个页面的效果。这种结构由client-side routing库 `react-router` 实现，这个库允许你基于浏览器的URL来渲染不同的组件，从而允许每个“View（页面，或者组件）”被当做独一无二的资源（[resource](https://en.wikipedia.org/wiki/Web_resource)）来对待。

## 单页App

React允许你基于不同的条件（比如 `state` 变量）来动态地渲染Views（组件）。

一个组件经常会被作为一个单独的页面存在而被当成独立的资源，所以它常常会有自己的URI（统一资源标识符，注意和URL区分！），这允许每个View被独立地引用。比如，一个博客中每篇博文都可以拥有自己的URI，用户可以输入特定的URL来浏览某篇博文。

服务器侧routing会返回整个HTML文件，并由该文件的JavaScript代码来决定显示什么资源。与服务器侧routing不同，在客户端侧的routing中，显示哪个View由JavaScript代码基于URL决定，而只需要向服务器请求某些特定的资源而非整个HTML页面，这使得加载的速度更快了。同时，这也允许你的App能够更方便地共享state数据和组件。

在函数组件中，你可以使用条件渲染（[conditional rendering](https://reactjs.org/docs/conditional-rendering.html) ）在route**匹配**的情况下渲染出对应的页面：

```jsx
function App(props) {
    //pick a component based on the URL
    let componentToRender = null;
    if(currentUrl === '/home'){ //pseudocode comparison with URL
        componentToRender = <HomePage />;
    }
    else if(currentUrl === '/about'){
        componentToRender = <AboutPage />;
    }
    //render that component
    return componentToRender;
}
```



## React-Router

[**React Router**](https://reactrouter.com/en/6.4.4) 等三方库提供了特定的组件，这些组件包含了**匹配**功能，使得开发单页应用非常方便。

> 这里仅介绍 [**version 6**](https://reactrouter.com/en/6.4.4) of React Router, released in *November 2021*. 该版本和之前的版本差别很大，所以当你寻找资源的时候请注意它们使用的版本！关于更早版本的React Router的资料, 请参考 [the documentation for those versions](https://v5.reactrouter.com/)。

使用npm安装React Router：

```bash
npm install react-router-dom
```

然后，在你的JS文件中引入这些组件：

```js
//import BrowserRouter, Routes, Route, and Link from react-router
import { BrowserRouter, Routes, Route, Link} from 'react-router-dom'
```

## Routing

[**`<BrowserRouter>`**](https://reactrouter.com/en/6.4.4/router-components/browser-router)组件，有时引入它时我们会将其命名为 `<Router>`，是React Router的“基底组件（base component”。

> 该组件负责使 React 应用程序的用户界面（例如渲染哪些组件）与浏览器的 URL 保持同步。BrowserRouter 会 "监听 "URL 的变化，然后将有关当前路由（称为路径）的信息传递给其子组件。这样，每个子组件就能随时知道 URL 中当前显示的路由，而无需直接访问 URL。

你的 App 有且只有一个 `<BrowserRouter>` 组件，我们一般将其置于 index.js 里，放在 `<App>` 组件的外面。

```jsx
//index.js

import { BrowserRouter } from 'react-router-dom'
import App from './components/App.js'

//render the App *inside* of the BrowserRouter
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
    <BrowserRouter>
        <App />
    </BrowserRouter>
);
```

在`<BrowserRouter>` 内（一般在App里），我们可以使用 [`<Route>`](https://reactrouter.com/en/6.4.4/route/route) 组件来指定不同 `path` 下需要渲染的Views。只有当前 URL 符合指定路径（path）时，该组件才会渲染其元素，就像 `if` 一样！

注意：在所有的`<Route>` 的外面要套一个 `<Routers>` （**注意 `s` 复数！**）。

```jsx
function App(props) {
    return (
        <Routes> {/* the collection of routes to match */}
            {/* if currentUrlPath === "home" */}
            <Route path="home" element={<HomePage />} />

            {/* if currentUrlPath ===  "about" */}
            <Route path="about" element={<AboutPage />} />
        </Routes>
    );
}
```

- Tips: 

  - 根符号 `/` 用于匹配无路径的 URL（例如，在 http://domain.com 上显示的内容）。

  - 在路径中使用通配符 `*`将匹配到 "任何内容"，我们可以用来用其来渲染“Pages Not Found”。

  - 路径也可包括多个分段（如：`main/info` ），但多个分段通常对应[嵌套路由](#nested-routes)，详见下文。

  - 需要被渲染的组件（View）被作为 `element` prop传递给`<Router>`。传递的语法比较复杂：将要渲染的`<Component/>` 放在一个 `{}` 里：`element={<AboutPage />}`

  - 你可以给要渲染的组件传递其他props，但这样不好看：

    ```jsx
    <Route path="profile" element={<ProfilePage user={userData} />} />
    ```

    如果你要传递很多props，建议给它们加一个“Wrapper”组件，使要传递的组件只有一个"Wrapper"。

## 兼容性

`BrowserRouter` utilizes the [HTML5 history API](https://developer.mozilla.org/en-US/docs/Web/API/History_API) to interact with the brower’s URL and history (what allows you to go “back” and “forward” between URLs). This API is supported by [modern browsers](https://caniuse.com/#feat=history), but older browsers (i.e., IE 9) would need to use `<HashRouter>` as an drop-in alternate. `HashRouter` uses the [fragment identifier](https://en.wikipedia.org/wiki/Fragment_identifier) portion of the URI to track what “page” the app should be showing, causing URL’s to include an extra hash `#` symbol in them (e.g., `https://mydomain.com/#/about`).

## Nested Routes

有时候，我们会需要再一个路径的不同子路径下渲染不同的组件，同时保证该父路径的某个组件在任何子路径的情况下都被渲染，比如 `/user` 路径下，我们都需要渲染 `<UserLayout />` 组件，但在子路径 `/user/profile` 下我们需要渲染 `<UserProfile />` 组件；而在子路径 `/user/favorites` 下，则需要渲染 `<UserFavorite />` 组件。 这时候，我们就可以使用嵌套路由来实现这种效果：

```jsx
// An example of nested routes
<Routes>
    <Route path="user" element={<UserLayout />} >
        <Route path="profile" element={<UserProfile />} />
        <Route path="favorites" element={<FavoriteItems />} />
    </Route>
    <Route path="items" element={ <ItemList />} />
</Routes>
```

我们使用 [`<Outlet />`](https://reactrouter.com/en/6.4.4/components/outlet) 在外层的 `<Route />` 的 `element` 的组件函数里代表嵌套路由将要替代的位置：

```jsx
function UserLayout(props) {
    render (
        <div className="user-layout">
            <h1>User Page</h1>
            {/* will be replaced with <UserProfile>, <UserFavorites>, or null */}
            <Outlet />
        </div>
    )
}
```

嵌套路由主要用于创建共享布局，如上例所示。`<UserLayout>` 组件可包含结构元素（如 div），这些元素将在以相同路径开始的路由中共享，但不会在其他路由中被共享。

> 并非所有的App都需要嵌套路由！如果你的整个应用只有单一的布局，你无需为此另外创建一个组件。你只需要在 `<App>` 里使用路由来表示动态变化的组件内容即可。

### 默认嵌套页面

如果你想要在嵌套路由里创建一个“默认”子组件，你可以给该子组件一个 [**`index`**](https://reactrouter.com/en/6.4.4/start/tutorial#index-routes) prop:

```jsx
<Routes>
    <Route path="user" element={<UserLayout />} >
        {/* show the UserHome at ``/user` */}
        <Route index element={<UserHome />} />
        <Route path="profile" element={<UserProfile />} />
        <Route path="favorites" element={<FavoriteItems />} />
    </Route>
</Routes>
```

> Notice that the `index` prop (which takes no other values!) in effect “replaces” the `path` segment for that child.

### 安全性

**注意：** `<route>` 只能用来针对对不同的URI显示不同内容，如果你需要根据状态来显示不同的内容（比如用户是否登录等），请使用基于state的 [conditional rendering](https://reactjs.org/docs/conditional-rendering.html)：

```jsx
function Greeting(props) {
  const isLoggedIn = props.isLoggedIn;
  if (isLoggedIn) {    return <UserGreeting />;  }  return <GuestGreeting />;}
const root = ReactDOM.createRoot(document.getElementById('root')); 
// Try changing to isLoggedIn={true}:
root.render(<Greeting isLoggedIn={false} />);
```



### 构建大型App时的建议

将路由声明为一个 const 的 `object`变量。

> It is often useful to specify the routes as a `const` variable (e.g., `routes`) that is an object containing paths and which component to render for that path. You can use the [`useRoutes()`](https://reactrouter.com/en/6.4.4/hooks/use-routes) hook to then render this object *instead* of specifying a `<Routes>` element. This is only recommended for particularly large applications.

You can use the [`useMatch()`](https://reactrouter.com/en/6.4.4/hooks/use-match) hook to get access to the current `path`; this can be useful for specifying e.g., relative image paths.



## URL参数

URL中可以在最后以 `:param` 来指定一个参数。比如:

```
https://api.github.com/users/:username
```

的 `username` 就是指向github里的一个特定的用户。`https://api.github.com/users/0xtopus` 的 `username`参数值就是 `0xtopus` 。

你可以在React Router里使用下列语法来支持URL参数：

```jsx
<Route path='posts/:postId' element={<BlogPost />} />
```

当URL和 `posts/` 匹配的时候，后面的参数会自动地被分配给变量 `postId` ，比如 `post/hello` 的`postId` 就是“hello”， `post/2022-10-31` 的 `postId` 就是"2022-10-31"。

React Router也支持多段参数，比如：

```jsx
 <Route path="posts/:date/:title" element={<BlogPost />} />
```

就有两个参数：`date` 和 `title` 。

你可以使用 `react-router` 提供的 [`useParams`](https://reactrouter.com/en/6.4.4/hooks/use-params) 钩子函数来获取URL参数。这个钩子函数会返回一个 `object` 类型的变量，它的key是URL变量的名称，值是当前URL变量的值；如果是多段参数，那么对象里就会有多个key和value对应不同的变量名和变量值：

```jsx
import { useParams } from 'react-router-dom';

function BlogPost(props) {
    //access the URL params as an object
    //it's also common to use object destructuring here
    const urlParams = useParams();

    return (
        {/* postId was the URL parameter from the above example! */}
        <h1>You are looking at blog post {urlParams.postId}</h1>
    )
}
```

### query param

如果你需要使用query parameters (e.g., the `?key1=value1&key2=value2` part of the URL)，你可以使用 [`useSearchParams()`](https://reactrouter.com/en/6.4.4/hooks/use-params) 钩子函数。它和state钩子函数很像，但值会被存在URL query parameter中而不是组件本身里。

记住，只有在变量值不与一个恒定资源（consistent resource）对应的时候（比如 search queries），我们才使用query parameter！

> 在 Web 开发中，查询参数是指出现在 URL 中的键值对，通常用于向服务器请求特定资源或者对资源进行过滤、排序或其他操作。在 React 应用程序中，可能会利用查询参数来获取或展示特定内容。
>
> 这段话强调了查询参数应该被用于像搜索查询等不对应于一致资源的数值。这意味着查询参数适合用于表示不同搜索条件下的内容、筛选或排序等功能，而不应该用于代表资源的唯一标识或属性。
>
> 换句话说，如果您的应用中有一些数据与特定资源相关联，例如单一的用户资料或者文章内容，那么最好使用其他方法，比如路由参数（route parameters）或者其他标识符来传递这些信息，而不是依赖查询参数。查询参数更适合用于处理与资源内容无关的事项，比如搜索查询、过滤条件等。
>
> 总的来说，这个提示的目的是指导开发者在使用 React 框架时，正确选择和使用查询参数，并避免滥用它们来表示资源的唯一标识或属性。



## Linking

使用了Router之后，我们就可以根据不同的URL来渲染出不同的页面了。但是，如何在不同的页面中切换呢？

如果直接使用 `<a>` 标签，那么每次跳转都会向服务器发送http请求，这过于麻烦。

我们可以使用 React Router 提供的 [`<link>`](https://reactrouter.com/en/6.4.4/components/link) 元素来达到只换URL来渲染另一个页面而不发送http请求的效果，该组件的效果和 `<a>` 标签十分类似，但它们不可以相互嵌套！

```jsx
<Link to="about">Click to visit the About Page</Link>
```

该组件使用props `to` 来表示要去到的URL的相对路径，如果你要返回上一级，可以使用 `..` 来表示。

你还可以使用React Router提供的 [`<NavLink>`](https://reactrouter.com/en/6.4.4/components/nav-link) 组件。该组件会给当前选中的link捆绑上一个类，因此你可以使用CSS来将其高亮显示，这通常被用于导航条来给用户指示当前所在的页面。你可以在文档中看到使用三元运算符来添加类名，或者你可以参考我自己写的这个例程：[NavLink示例](https://playcode.io/1698238)

## 重定向

有时候，无需用户操作，页面也需要自动跳转到另一个URL，比如当用户提交完表单后，一般会自动重定向到首页。

这时可以使用 [**`useNavigate()`**](https://reactrouter.com/en/6.4.4/hooks/use-navigate) 钩子函数。当调用这个函数的时候会创建并返回另一个函数，按惯例我们会给其命名为一个常量 `navigate`，当调用 `navigate()` 时，我们将需要重定向到的URL路径（类似 `link` 里的 `to` props的值）当成参数传递给它并触发重定向。

```jsx
import { useNavigate } from 'react-router-dom';

//A simple form component
function Form(props){
    const navigate = useNavigate(); //access navigate function

    //event handler for the form
    const handleSubmit = function(event) {
        event.preventDefault();
        //do form submission work here

        navigate("/home") //navigate to the `/home` route
    }

    return (
      <form onSubmit={handleSubmit}> ... </form>
    )
}
```

**注意：仅在事件的回调函数里调用 `navigate` 函数！**不要在组件函数内部使用！在组件内部，如果你需要重定向，请使用 [`<Navigate>`](https://reactrouter.com/en/6.4.4/components/navigate)  元素！该元素也需要一个 `to` props来指示重定向后的页面，就像link一样！

一般很少使用 [`<Navigate>`](https://reactrouter.com/en/6.4.4/components/navigate)  元素，除非你确定该跳转操作不需要任何用户操作。一般的，不要在显示的内容内部（比如 `<div>` ）使用 [`<Navigate>`](https://reactrouter.com/en/6.4.4/components/navigate)  元素；而是**确定你在什么情况下需要跳转**，然后用 `if` 语句在该情况下return [`<Navigate>`](https://reactrouter.com/en/6.4.4/components/navigate)  元素。

一个有用的例子就是只对已登录的用户显示内容：

```jsx
function ProtectedPage(props) {
  //...determine if user is logged in (eg., via AJAX)

  if(!userIsLoggedIn) { //if no user, send to sign in
    return <Navigate to="/signin">;
  }

  //otherwise show content
  return (
    <div>protected content!</div>
  )
}
```

我们可以进一步将授权访问封装成一个组件，以达到复用的效果。然后将需要授权才能访问的内容放在[Nested Routes](#Nested-Routes)里：

```jsx
function RequireAuth(props) {
  //...determine if user is logged in (eg., via AJAX)

  if(!userIsLoggedIn) { //if no user, send to sign in
    return <Navigate to="/signin">;
  }
  else { //otherwise, show the child route content
    return <Outlet />
  }
}

function App(props) {
  return (
    <Routes>
      {/* protected routes */}
      <Route element={<RequireAuth />}>
        <Route path="profile" element={<ProfilePage />} />
        <Route path="secret" element={<SecretPage />} />
      </Route>
      {/* public routes */}
      <Route path="signin" element={<SignInPage />} />
    </Routes>
  )
}
```

注意， `<RequireAuth>` 没有提供 `path` props，所以只要当它的子组件URL匹配时，该组件就会自动被渲染，启动授权判断。

## React Routing和Hosting

当您希望在非开发服务器（如 Github Pages）上部署应用程序时，React Router 的客户端路由有一些需要额外注意的事项（例如，部署 create-react-app 项目时会发生的情况）。

首先，思考一下在浏览器的 URL 栏中输入路径（如 `https://domain.com/about` 访问 /about 路径时）以导航到该路由时会发生什么。这将在带有 /about 路径的 URI 上为资源创建一个 HTTP 请求。当网络服务器收到该请求时，服务器将执行**服务器端路由**（*server-side routing*），并尝试访问该位置的资源（例如，它将查找  `/about/index.html` 页面）。但这并不是你想要的结果--因为该资源并不存在，服务器将返回 404 错误。

但实际上你想要服务器在收到请求后返回根目录下的 `/index.html` ，以及并添加适当的 JavaScript 代码，这样客户端路由就能更改浏览器的 URL 栏，并在 `/about` 路由上显示内容。

如果你有一个web server，那么这是完全可能的（to *not* perform server-side routing and instead always return `/index.html` no matter what resource is requested）; indeed, this is what the Create React App development server does.

但 GitHub Pages 做不到这点。不过，你依然有几种方法来处理这个情况：

1. 您可以使用 [`<HashRouter> `](https://reactrouter.com/en/6.4.4/router-components/hash-router)  代替 `<BrowserRouter>`： `<HashRouter>` 使用 URI 的片段标识符部分来记录和跟踪用户正在查看的路由。因此，HTTP 请求被发送到 `https://domain.com/index.html#/about`以获取 /about 路由。由于 index.html 是默认资源，因此可以缩写为 `https://domain.com/#/about`。

   通过这种方式，您可以始终请求适当的资源（`/index.html`），但仍可执行客户端路由。但不推荐这种方法，因为您的 URL 中会出现多余的 # 符号（这会使使用片段进行内页导航变得更加困难），而且访问 `domain.com/about` 仍会导致 404 错误。



2. 另一种方法是将服务器的 404 页面替换为指向您的 `index.html`（使用服务器端路由）的页面。[spa-github-pages](https://github.com/rafrex/spa-github-pages) 提供了一些使用 GitHub Pages 执行此操作的模板，但这是一种 "笨拙 "的方法，因此也不推荐使用。



3. **最好的方法**是使用能更好支持单页面应用所需的服务器端路由的托管系统（web hosting system）。

   比如，[Firebase Hosting](https://firebase.google.com/docs/hosting/) 是提供网站托管服务的一种解决方案，它允许您指定重写规则，无论 HTTP 请求指定的是哪个路由，服务器都会返回您的 `index.html`。这种配置允许单页应用的路由逻辑由客户端处理，而服务器始终返回同一个 HTML 文件，然后客户端应用程序负责加载并处理不同的路由。

   > Create React App provides [instructions](https://create-react-app.dev/docs/deployment/#firebase) on deploying to Firebase Hosting, including a simple wizard configuration that allows you to configure your site as a single-page application.



