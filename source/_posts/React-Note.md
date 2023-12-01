---
title: React Note
date: 2023-12-01 21:10:42
tags:
---

# React简介

参考：[Introduction to React](https://info340.github.io/react.html)

React 是目前用于构建大型网络应用程序的最流行的 "框架"，其拥有数以百计的教程、视频和示例用于构建 React 应用程序。建议从[官方文档](https://reactjs.org/docs/getting-started.html)开始学习，尤其是[main concepts](https://reactjs.org/docs/hello-world.html)。本章或多或少沿用了 Facebooks 的 [Intro to React Tutorial](https://reactjs.org/tutorial/tutorial.html) 所使用的方法来进行叙述。

## Get Started

在使用React构建web应用程序的时候，需要用到大量开发工具，如果自行搭建开发环境非常费时费力。

因此，我们可以使用 [**Create React App (CRA)**](https://create-react-app.dev/) 命令行工具历来搭建我们的React应用脚手架，它提供内置的的代码、测试以及部署方案。

使用包管理器 `npm` 来调用CRA命令：

```bash
# Create a new React app project in a new `my-app` directory
# This may take a minute...
npx create-react-app my-app --use-npm

# Change into new project directory to run further commands
cd my-app
```

- [npx](https://www.npmjs.com/package/npx) 命令与 `npm 5.2.0` 及更高版本一起安装，可用于（临时）安装和运行全局命令，一步到位。因此，与其使用 `npm install -g create-react-app`，不如直接使用 `npx`。

- 参数 `--use-npm` 是可选的，但最好加上这句，因为这指定了使用 `npm` 而不是其他的包管理器。

### 运行开发服务器

创建 React 应用程序后，您可以使用 CRA 提供的脚本之一（启动脚本）自动启动开发网络服务器：

```bash
# Make sure you are in the project directory
cd path/to/project

# Run the development server script
npm start
```

上述命令会打开网络服务器，并自动打开一个新浏览器页面来展示渲染的页面！

该网络服务器在代码发生任何改动的时候会自动执行下列操作：

1. 将React代码 **transpile** 为纯 JavaScript；
2. 将所有不同的modules打包(**bundle**) 到同一个文件里并将该文件注入HTML中 (adding the `<script>` tag for you)；
3. 在浏览器界面里展示错误和警告：大部分错误和警告在console里展示，但严重的致命错误会直接显示在页面里 (e.g., syntax errors that crash the whole app)。如果你使用 [Chrome React Developer Tools](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=en) 能进一步提供更多错误和调试信息；
4. 只要源代码发生任何变动，它会帮你自动重载页面 (Though it is often good to manually refresh things when testing, just to make sure)；

上述能力是由一个叫 [**webpack**](https://webpack.js.org/) 的工具提供的，CRA已经帮你配置好相关的设置了，所以你可以直接使用它。

> **存在的问题：**
>
> In order to stop the server, hit `ctrl-c` on the command line.
>
> **Important**: As of 2018, there is an [issue](https://github.com/facebook/create-react-app/issues/932#issuecomment-433596579) with Create React App on Windows using Git Bash where hitting `ctrl-c` won’t actually stop the server—if you try to run it again, you’ll get an error about the port still being occupied. Current work-arounds are to use the Task Manager to end the `node` process, or to instead use the [Linux subsystem for Windows](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide).

## 项目结构

新建的React项目有以下结构：

- `public/index.html`是页面的home page。其并不包含任何 CSS `<link>` 或 JavaScript `<script>` ，因为它们仅在构建页面的时候才被自动注入到HTML中。 项目的 CSS 和 JavaScript 都在 `src/` 文件夹下。 所有页面的内容都被定义为JavaScript的组件，即整个页面都是提供JavaScript来构建的。因此，除了改变诸如 `<title>` 和 favicon 等元数据以外， `index.html` 基本不需要作任何改动。

  `index.html` 里几乎没有任何 HTML 内容，除了 `<head>`,  `<body>` 和一个 `<div id="root">`。这是因为整个应用都将使用 React JavaScript 来构建，所有内容都会在稍后被注入 `#root` 元素内。

  

- `public/` 文件夹内存放所有不通过JavaScript加载但是是页面所需的文件 (i.e., they’re not “source code”)。比如，  `img/` 文件夹就位于此处，用来放置所有页面中所需的图片。 `public/` 文件夹是网页的“根目录”， 位于 `public/img/picture.jpg` 的图片在DOM中可以表示为： `img/picture.jpg`。详见： [Using the public folder](https://create-react-app.dev/docs/using-the-public-folder/) 。



- `src/` 文件夹是放置 React app 源代码的地方。整个App由 `index.js` 脚本启动，并引入其他 `.js` 文件构建的JavaScript模组。一般而言，我们会用一个或多个modules(e.g., `App.js`, `AboutPage.js`)来实现组件, 这些组件稍后会被引入 `index.js` ；

  - 默认模块 `reportWebVitals.js` 和 `setupTests.js` 分别用来支持测试和性能评估

  

- `src/index.css`  和 `src/App.css` 文件分别负责整个页面的CSS和单个组件的CSS。注意CSS文件在JavaScript文件里被引入 (with e.g., `import './index.css'` in the `src/index.js` file)。webpack知道如何处理引入的CSS文件，所以你不用费心再把它们link到HTML里。

  如果你需要引入外部的样式，比如 Bootstrap 和 Font-Awesome，你可以在 `public/index.html` 里link它们，但最好的做法是将它们作为modules安装，并通过JavaScript来引入它们。具体做法可以参考文档： [*Adding Bootstrap*](https://facebook.github.io/create-react-app/docs/adding-bootstrap) 。

  - 注意：所有CSS规则都是全局的，但为了保持项目结构清晰，CSS被打散到了不同的文件中。如果你不希望CSS被全局应用，见 [CSS in JS](https://info340.github.io/css-in-js.html#css-in-js)；

- `README.md` 包含了 [User Guide](https://facebook.github.io/create-react-app/docs/documentation-intro) ，告诉你一些基本任务是如何实现的。在你想实现任何东西之前可以先看看这里是否有你需要的东西！

> Overall, you’ll primarily be working with the `.js` and `.css` files found in the `src/` folder as you develop a React app.



## JSX

React 的核心是一个简单的 DOM 创建和呈现库--它允许您声明新的 DOM 元素（例如，创建一个新的 `<h1>`），然后将该元素添加到网页中。这与您使用 DOM 或 jQuery 库的过程类似。在React中，有一系列的函数来实现这个功能：

```react
//Import required functions from the React libraries
import React from 'react';
import ReactDOM from 'react-dom/client';

//Create a new `<h1>` element using React methods. This function takes
//arguments indicating what the element should be
const msgElem = React.createElement(
  //html tag
  'h1',
  //object of attributes
  { id: 'hello', className: 'myClass' },
  //content
  'Hello World!'
);

//Create a "React root" out of the `#root` elemment
const root = ReactDOM.createRoot(document.getElementById('root'));

//render the React element at that root
root.render(element)
```

- `React.createElement()` 允许你创建元素，`ReactDOM.createRoot()`给渲染React元素创造了环境(context)，经常的，在`div#root` 里创建。`root.render(element)` 将把创建的root里的内容替换为element的内容。

  > 注意 `class` 属性在创建时被标记为 **`className`** 。这是为了防止和JavaScript的 `class` 关键字冲突。

> The `createRoot()` function was introduced with React v18 (March 2022). Previous versions of React used the `ReactDOM.render()` function directly instead of explicitly creating a root context. Using `ReactDOM.render()` will cause your app to function as if using React v17, preventing some advanced features from working.

你或许发现了， `React.createElement()` 括号内的内容非常复杂，因此，React允许你使用JSX，一种JavaScript的语法扩展来在JavaScript里直接写HTML标签：

```jsx
//Create a new `<h1>` element using JSX syntax
const element = <h1 id="hello" className="myClass">Hello World</h1>;

//Create a "React root" out of the `#root` elemment
//then render the React element at that root
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(element)
```

> 注意 `class` 属性在创建时被标记为 **`className`** 。这是为了防止和JavaScript的 `class` 关键字冲突。

分配给元素变量的值不是字符串（没有引号）。相反，它是调用 `React.createElement()` 函数的简写 -- 类似 HTML 的语法被自动转换为实际的 JavaScript 方法调用，从而让书写更简单。

**注意：** 依然要在最后加上 `;` ！！！

JSX也可以定义多个元素，只需加上一对括号即可：

```jsx
//JSX can define nested elements
const header = (
  <header className="banner">
    <h1>Hello world!</h1>
  </header>
);
```

**注意：**类似于XML，JSX也要求所有元素都被“关闭”。因此，你必须在空元素最后加上一个 `/`：

```jsx
//JSX elements must be closed
const picture = <img src="my_picture.png" alt="a picture" />
```

实际上，JSX并非真正的HTML，只是在Webpack构建的时候，其会自动调用一个叫做 [**Babel**](https://babeljs.io/) 的工具来 transpling 相关的代码。同样的，CRA也已经帮我们配置好了。

- Yes, chaining tools together like this is [how modern web development works](https://hackernoon.com/how-it-feels-to-learn-javascript-in-2016-d3a717dd577f).

### 行内表达式

JSX也允许你在HTML里使用JavaScript表达式：

```jsx
const message = "Hello world!";
const element = <h1>{message}</h1>;
```

任何表达式都可以！

```jsx
//Including an inline expression in JSX. The expressions can be mixed directly
//into the HTML
const element = <p>A leap year has {(365 + 1) * 24 * 60} minutes!</p>;
```

你还可以定义一个 `Object`定义一堆属性，然后使用*spread operator* (`...`)把它们赋给HTML：

```jsx
//Define a JavaScript variable
const imgUrl = 'path/to/my_picture.png';

//Assign that variable to an element attribute
const pic = <img src={imgUrl} alt="A picture" />;

//Define an object of element attributes
const attributes = {
    src: 'path/to/other_picture.png',
    alt: 'Another picture'
};

//A DOM element that includes those attributes, among others.
//The spread operator applies the property names as attributes of the element.
const otherPic = <img {...attributes} className="my-class" />;
```

同样的，这些表达式也可以进行嵌套，实现HTML元素的嵌套：

```jsx
//Define variable content to display. One is a string, one is a React element.
const greetingString = "Good morning!";
const iconElem = <img src="sun.png" alt="A wide awake sun" />;

//Conditionally change values based on an `isEvening` value (defined elsewhere)
if(isEvening) {
    greetingString = "Good evening!";
    iconElem = <img src="moon.png" alt="A dozing moon" />;
}

//Include the variables inline in another React element
//Notice how the `icon` element is included as a child of the `<header>`
const header = (
    <header>
      <h1>{greetingString}</h1>
      {iconElem}
    </header>
);
```

数组里的一组行内元素会被当成父元素底下的一组兄弟元素：

```jsx
//An array of React elements. Could also produce via a loop or function
const listItems = [<li>lions</li>, <li>tigers</li>, <li>bears</li>];

//Include the array as an inline expression; each `<li>` element will be
//included as a child of the `<ul>`
const list = (
  <ul>
    {listItems}
  </ul>
);
```

最后，如果你要在JSX里写注释，只能使用 `/* */` 这种写法，使用HTML的` <!-- -->` 和JavaScript的行注释 `//` 都是无效的！

```jsx
const main = (
    <main>
      { /* A comment: the main part of the page goes here... */ }
    </main>
)
```



## React组件

使用React构建页面的时候，通常使用组件来搭建页面。

组件就像是页面中的一小块，它们可以通过相互组合和嵌套来组成复杂的页面。

### 定义组件

组件通常被定义为函数，但是一般以帕斯卡命名法来命名（PascalCase）：

```jsx
//Define a Component representing information about a user
function UserInfoCard(props) {
  //This is an everyday function; you can include any code you want here
  //including variables, if statements, loops, etc
  const name = "Ethel";
  const descriptor = "Aardvark";

  //Return a React element (JSX) that is how the Component will appear
  return (
    <div>
      <h1>{name}</h1>
      <p>Hello, my name is {name} and I am a {descriptor}</p>
    </div>
  )
}

//Create a new value (a React element)
//This syntax in effect "calls" the function
const infoElement = <UserInfoCard />;

//Show the element in the web page (inside #root)
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(infoElement);
```

因此，当你看到某个JavaScript函数的名字是驼峰命名法命名的时候，你就知道这是一个React组件。

- 组件函数的返回值通常是一个JSX，请尽可能将返回的JSX写得简洁明了，如果有需要进行复杂的代码表达式运算、逻辑运算、循环等等，请在返回值外的函数体内进行。

- 也有一些库会将组件写成匿名函数并赋值给一个 `const` 变量，不过处于易读性的考虑不推荐这样做：

```jsx
const Example = (props) => {
  return <div>...</div>
}
```

- 组件通常会接入一个参数，这个参数按惯例被命名为 `props`，详见下面的[介绍](#Props)

- 组件函数的返回值一般用一个 `<div>` 包裹，当然，在有些情况下，如果你不希望用div，你可以使用[Fragment](https://reactjs.org/docs/fragments.html)来包裹返回值：

  ```jsx
  function FragmentExample(props) {
    return (
      <>
        <p>First paragraph</p>
        <p>Second paragraph</p>
      </>
    )
  }
  ```

- 当组件被定义后，就可以使用XML标签的格式来使用它 (i.e., as `<UserInfoCard />`)，**不要直接调用组件函数！！！**（比如 `UserInfoCard()` ）

  

- 组件也可以相互嵌套使用，非常灵活：

  ```jsx
  //A component representing a message
  function HelloMessage(props) {
    return <p>Hello World!</p>;
  }
  
  //A component representing another message
  function GoodbyeMessage(props) {
    return <h1>See ya later!</h1>;
  }
  
  //A component that is composed of (renders) other components!
  function MessageList(props) {
    return (
      <div>
        <h1>Messages for you</h1>
        <HelloMessage /> {/* include a HelloMessage component */}
        <GoodbyeMessage /> {/* include a GoodbyeMessage component */}
      </div>
    );
  }
  
  //Render an instance of the "top-level" ("outer") component
  const root = ReactDOM.createRoot(document.getElementById('root'));
  root.render(<MessageList />);
  ```

实际上，React 应用程序是由许多不同的组件（Components）组成的，根据应用程序的大小，组件的数量可达几十个甚至上百个！最顶部的组件通常称为 `App`，App 的返回值会实例化更多子组件，每个子组件代表页面的不同部分。

`App` 组件将代表网站的全部内容，其他组件将作为 `<App>` 的子组件。但是，由于 React 组件将被插入到 `index.html` 文件中的 `<div id="root">` 元素内，你不需要在 React 组件中包含 `<body>` 或 `<head>` 元素 ---- 这些元素已经在 HTML 文件中定义好了，因此不会由 React 创建！

### Strict Mode

在CRA提供的初始代码里，你会发现 `<App>` 组件被嵌在 `<React.StrictMode>` 组件中：

```jsx
import React from 'react';
import ReactDOM from 'react-dom/client';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

 [**`StrictMode`**](https://reactjs.org/docs/strict-mode.html) 组件由React对象提供，就像JavaScript的 `use strict`  声明一样，这个组件会激活很多React内置的错误检查，尽管这些错误检查有些有点过时，且会使一些 `console.log` 输出多次，但还是很有用。



## 组件的层次结构

组织组件的层次结构的原则是：使你能快速地找到你的代码，并使你的App的结构简洁易懂！

你可以把不同的模组分开，也可以放在同一个文件中。一种可能的结构如下：

```bash
src/
|-- components/
  |-- App.js
  |-- navigation/
    |-- NavBar.js
  |-- utils/
    |-- Alerts.js
    |-- Buttons.js
index.js
```

记住，组件是用函数来表示的，因此你需要使用 `import` 和 `export` 来引入和导出它们。组件之间不要相互包含！而是应该有一定的层次结构，一直向上到 `index.js` ：

```jsx
/* in Messages.js file */

//Export the defined components (as named exports)
export function HelloMessage() { /* ... */ }

export function GoodByeMessage() { /* ... */ }
```

```jsx
/* in App.js file */

//Import components needed from other modules
import { HelloMessage, GoodbyeMessage } from `./Messages.js`; //named imports!

//Can also export as a _default_ export; common if the file has only one component
export default function App() {
    return (
        <div>
            {/* Use the imported components */}
            <HelloMessage />
            <GoodbyeMessage />
        </div>
    )
}
```

```jsx
/* in index.js file */

//Import components needed from other modules
import App from `./App.js`; //default import!

//Render the imported component
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App />);
```

注意，`index.js` 通常只会引入一个组件。

## class组件

**当前定义组件最好的方法就是使用函数组件！** 但是，为了适应使用老版本的React写的代码，最好了解一下class组件。

在更老的版本中，React组件也可以用 `class` 关键字来声明。这个类*继承*了 [**`React.Component`**](https://reactjs.org/docs/react-component.html) 类 -- 这表示你定义的其实是一个React Component!：

```jsx
//Define a class component representing information about a user
class UserInfo extends React.Component {
    //Components MUST override the `render()` function, which must return a
    //DOM element
    render() {
        //This is an everyday function; you can include any code you want here
        const name = "Ethel";
        const descriptor = "Aardvark";

        //Return a React element (JSX) that is how the component will appear
        return (
            <div>
                <h1>{name}</h1>
                <p>Hello, my name is {name} and I am a {descriptor}</p>
            </div>
        )
    }
}

//instantiate the class as a new value (a React element)
const infoElement = <UserInfo />;

//Show the element in the web page (inside #root)
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(infoElement);
```

这和之前使用函数来定义组件非常类似，你只需把函数体换成类里的方法 `render()` 即可。

- 注意： `React.Component` 类经常在引入时被赋了别名，所以你可以直接使用它的别名:

  ```jsx
  import {Component} from 'react';
  
  class MyComponent extends Component {
      render() {
          //...
      }
  }
  ```

> Additionally, older versions of React included a function `React.createClass()` that took in an *object* whose properties were functions that would act as class methods (see [here](https://toddmotto.com/react-create-class-versus-component/) for details). Although this function has been removed, it’s worth being aware of in case you come across older examples of React code that you need to interpret or adapt.

## props

使用React组件的好处是往往我们能够向组件中传递一组现成的数据，然后在页面中渲染出我们想要的效果。当这些数据发生改变时，组件也会将页面重新渲染。

函数组件会接入一个 `Object` 类型的参数，按惯例命名为 `props` （properties）。

在创建组件的时候，可以用 `propName="value"` 的形式向组件传递props。因为是JavaScript的标识符，所以用驼峰命名法（camelCase）：

```jsx
//Passing a prop called `message` with value "Hello property"
const messageA = <MessageItem message="Hello property!" />;

//Passing a prop using an inline expression
const secret = "Shave and a haircut";
const messageB = <MessageItem message={secret} />;

//A component can accept multiple props
//This component takes in a `userName` prop as well as a `descriptor` prop
const userInfo = <UserInfo userName="Ethel" descriptor="Aardvark" />;
```

虽然你可以多传或少传props，但一般组件会定义它们所需要的props，最好和其保持一致。

并且，组件在拿到props之后，一般会使用 Object Destructing 把props对象里的键值转换成变量：

```jsx
function UserInfo(props) {
    //assign the prop object properties to variables
    const {userName, descriptor} = props;

    return <h1>{userName}</h1>;
}
```

另一种的destructuring的方法：

```jsx
//The props object passed in will be destructured into two argument variables
function UserInfo({userName, descriptor}) {
    return <h1>{userName}</h1>;
}
```

但为了保持简洁，之后的例子将不做destructuring，而是将props直接使用。

props可以为任意类型，数组、函数，甚至另一个组件也没问题！

```jsx
//Props can be of any data type!

//Pass an array as a prop!
const array = [1,2,3,4,5];
const suitcase = <Suitcase luggageCombo={array} />;

//Pass a function as a prop (like a callback)!
function sayHello() {
    console.log('Hello world!');
}
const greeting = <Greeting callback={sayHello} />;

//Pass another Component as a prop (not common)!
const card = <HolidayCard message="Greetings world!" />
const gift = <Gift toMsg="Ethel", fromMsg={card} />
```

实际上，将回调函数作为props传递给其他组件是构建interactive React 应用的一种常见做法。

### props的传递

React的组件经常嵌套着使用，这意味着在一个组件中返回的JSX里包含有其他子组件。这些子组件的props需要通过父组件来向下传递给它们，即数据是自顶向下一层层**单向**传递的。

```jsx
//Defines a component representing a message in a list
function MessageItem(props) {
    return <li>{props.message}</li>
}

//A component that renders a trio of messages
function MessageListTrio(props) {
    const messages = props.messages; //assign prop to a local variable for convenience

    return (
        <div>
            <h1>Messages for you</h1>
            <ul>
                {/* instantiate child components, passing data from own props */}
                <MessageItem message={messages[0]} />
                <MessageItem message={messages[1]} />
                <MessageItem message={messages[2]} />
            </ul>
        </div>
    );
}

//Define and pass a prop to the parent comment when rendering it
const messagesArray = ["Hello world", "No borders", "Go huskies!"];
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<MessageListTrio messages={messagesArray} />);
```

在React中，创建List的常用方法就像上述代码所示那样：先给单个的列表对象创建一个子组件(e.g., `MessageItem`)，然后再给所有的单个列表项创建一个总组件(e.g., `MessageList`)来包含它们。

在传输含有大量数据的数据集，比如数组的时候，我们就可以使用 `map()` 函数来将父组件拿到的数据集分别拆分给一个个子组件：

```jsx
//Map an array of message strings to an array of components
const msgItems = props.messages.map((msgString) => {
    const component = <MessageItem message={msgString} />; //pass prop down!
    return component; //add this new component to resulting array
})
```

由于JSX会将一个作为行内元素的含有组件的数组渲染为相同的兄弟元素，所以创建列表变得非常方便，不需要写额外的循环：

```jsx
function MessageList(props) {
  //the data: an array of strings ["", ""]
  const messageStringArray = props.messages;

  //the renderable content: an array of elements [<>, <>]
  const msgElemArray = messageStringArray.map((msgString) => {
    const component = <MessageItem message={msgString} />; //pass prop down!
    return component; //add this new component to resulting array
  })

  return (
    <div>
      <h1>Messages for you</h1>
      <ul>
        {/* render the array of MessageList components */}
        {msgElemArray}
      </ul>
    </div>
  );
}

//Define and pass a prop to the parent comment when rendering it
const messagesArray = ["Hello world", "No borders", "Go huskies!"];
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<MessageList messages={messagesArray});
```

但是，上面这种写法会触发console的警告，原来，我们还要给每个子组件分别分配一个 `key` props，否则系统将无法追踪每个组件。（类似HTML的 `id` 属性，其实如果不指定，系统也会默认将组件在数组中的index作为 `key` 分配，但这样在数组元素发生变化的时候任意造成混乱，所以建议还是手动指定 `key` 为妙）。

现在，我们用对象数组来包含 `key` ：

```jsx
const MESSAGE_DATA = [
    {content: "Hello world", id: 1}
    {content: "No borders", id: 2}
    {content: "Go huskies!", id: 3}
];

function MessageList(props) {
  //the data: an array of objects [{}, {}]
  const messageObjArray = props.messages;

  //the renderable content: an array of elements [<>, <>]
  const msgElemArray = messageObjArray.map((msgObject) => {
    //return a new MessageItem for each message object
    //attributes are listed on their own lines for readability
    return <MessageItem
              message={msgObject.content}
              key={msgObject.id.toString()} {/* pass in a key prop! */}
              />;
  }) //end of .map()

  return (
    <div>
      <h1>Messages for you</h1>
      <ul>
        {msgElemArray}
      </ul>
    </div>
  ); //end of return
}

//Define and pass a prop to the parent comment when rendering it
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<MessageList messages={MESSAGE_DATA});
```







## 其他资料

组件思维：如何使用React来构建界面 https://react.dev/learn/thinking-in-react

React小项目：Tutorial: [Tic-Tac-Toe](https://react.dev/learn/tutorial-tic-tac-toe)

