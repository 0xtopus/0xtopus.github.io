---
title: AJAX请求
date: 2023-12-27 21:04:40
tags:
---



参考：https://info340.github.io/ajax.html#fetching-data

# AJAX请求

欲读此章，请先了解RESTful Web APIs，可以参考[这里](https://info201.github.io/apis.html#accessing-web-apis)。

本篇介绍了如何使用JavaScript来发送http请求，以及当需要发送web请求和执行其他任务时如何使用*异步编程*技术。

## AJAX简介

一般地，http请求由浏览器发送。但现代动态网页需要使用JavaScript来使页面无需用户操作浏览器发送请求而能够自动地更新。这种能够绕过浏览器发送请求的技术叫做“AJAX”（Asynchronous JavaScript and XML）。虽然现在更倾向于使用 JSON （JavaScript Object Notation）而非 XML 来表示数据，但由于前者不好发音所以我们仍然叫其“AJAX”。

## JSON

XML的语法过于复杂，这使得用XML来表示数据体量太大而且很难解析。于是，JavaScript的开发者发明了 JSON，它的语法和JavaScript的对象非常相似：

```json
{
  "firstName": "Alice",
  "lastName": "Smith",
  "favorites": {
    "music": "jazz",
    "food": "pizza",
    "numbers": [12, 42]
  }
}
```

但是，它的语法比JavaScript更加严格，以下是需要特别注意的几点：

- JSON 总是在 top-level 定义对象（`{}`）；
- JSON 不可以有多余的逗号或其他字符；
- JSON没有注释；
- JSON的 key必须用**双引号**括起来
- JSON中不可以包含函数，JSON的 value只能是**字符**，**数字**，**布尔值**（`true` 或者 `false`），**数组**（`[]`），或者**对象**；

JavaScript也提供了一个对象 `JSON` ，你可以使用里面提供的方法来将 JSON解析为对象，或者将对象转换成 JSON：

```js
//convert from Object to encoded String
let personObj = {firstName:"Alice", lastName:"Smith", id:12} //JavaScript object
let personString = JSON.stringify(personObj); //turn object into JSON string
console.log(personString); //=> '{"firstName":"Alice","lastName":"Smith","id":12}'
console.log(typeof personString); //=> 'string'

//convert from encoded String to Object
let favoritesString = '{"music":"jazz", "numbers":[12,42]}'; //a string, not an object!
let favoritesObj = JSON.parse(favoritesString); //turn JSON string into object
console.log(favoritesObj); //=> { music: 'jazz', numbers: [ 12, 42 ] }
console.log(typeof favoritesObj); //=> 'object'
```

- **注意：**如果你的 JSON 格式没有写对，那么 `JSON.parse()` 函数就会抛出一个 `SyntaxError`。如果 JSON 文档很长，那么有时候很难发现哪里出了错误，一些[在线工具](https://jsonformatter.curiousconcept.com/)也许能帮上忙。

## Fetching Data

浏览器内置有支持AJAX的全局变量 `XMLHttpRequest`。但这个API用起来**非常复杂**；使用 JQuery 也许能避免一些复杂性，但随着 JQuery 正在[被逐渐淘汰](https://youmightnotneedjquery.com/)，咱可以使用现代浏览器提供的 [**`fetch()`**](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) API 来发送AJAX请求！

你只需往 `fetch()` 函数里传入URL，即可下载对应的数据：

```js
fetch('https://domain.com/data');
```

- **注意：**在某些浏览器中，你将被禁止在  `file://` protocol (e.g., by double-clicking on the `.html` file) 里发送AJAX请求，这是为了防止你不小心运行了某些带有恶意代码的 `html` 文件。
- 记住，一定要使用 **web server** 来发送AJAX请求！（比如本地运行的 `live-server`）

还有，记得使用**相对路径**来指出你所需要fetch的文件或API的位置。这里的相对路径指的是<u>相对当前浏览的 **`.html`** 文件的路径！</u>不要误以为是相对当前调用 `fetch()` 的 `.js` 文件的相对路径！

### 向前兼容

如果你需要支持没有内置 `fetch()` 的旧版的浏览器，使用CDN引入外部库[**polyfill**](https://en.wikipedia.org/wiki/Polyfill)来引入 `fetch()`：

```html
<!-- put this BEFORE your own script! -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/fetch/2.0.3/fetch.min.js"></script>
```



## 异步编程

当你使用 `fetch()` 来下载数据的时候，数据并非一步到位，而是需要一点时间来下载。因此，调用 `fetch` 函数只能说是数据下载的**”开始“**，而在下载数据的时候，我们的JavaScript解释器会继续向下执行其他代码，而我们并不知道啥时候能把数据下载完成，此即”异步“：

```js
console.log('About to send request'); //statement 1

//send request for data to the url
fetch(url);  //statement 2

console.log('Sent request'); //statement 3

//The data is actually received sometime later,
//when the JS interpreter is down here!
```

`fetch()` 函数会返回一个对象变量  [**Promise**](https://developer.mozilla.org/en-US/docs/Mozilla/JavaScript_code_modules/Promise.jsm/Promise) 。Promise对象内就包含着暂时还不能获取的（因为还要等待下载完成）的数据的值 -- 你可以认为这就像是一个真正的“Promise”：它承诺在未来给你某些数据，但是承诺既可以被兑现，也同时有可能被打破！

> Promises are the modern way of handling asynchronous functions, but as part of the ES6 standard they are [not yet available to all browsers](http://caniuse.com/#feat=promises) (specifically: Internet Explorer). So you’ll need to include [*another polyfill*](https://github.com/stefanpenner/es6-promise) to support IE. This is also available from a [CDN](https://cdnjs.com/libraries/es6-promise).

Promise 有三种状态：

- Pending：等待数据下载完成；
- Fulfilled：数据下载完成；
- Rejected：数据获取失败；

我们可以使用不同的方法来处理不同状态下的Promise：

- 当Promise为Fulfilled的时候，使用 `.then()` 方法来对数据进行处理，给该方法传递一个回调函数（往往是匿名函数！）来告诉计算机当数据成功获取后该做些什么：

```js
function onSuccessCallback(response) { //what to do when we get the response
    console.log(response);
}

//When fulfilled, execute the callback function (which will be passed the response)
let promise = fetch(url);
promise.then(onSuccessCallback);

//It is much more common to use anonymous variables/callbacks:
fetch(url).then(function(response) {
    console.log(response);
});
```

注意：传递的回调函数通常具有一个变量，即我们需要获取的数据值。这是一个对象，代表着对http请求的回复，你可以使用一些keys来获取其内部的值：

```js
let promise = fetch(url);
promise.then(function(response){
    console.log( response.url ); //a string of where the request was sent
    console.log( response.status ); //the HTTP status code (e.g., 200, 404)
});
```

该对象拥有一个 `body` 属性，代表了HTTP请求应答的主体（data content）。但这个属性存储的数据是纯纯的 `0` 和 `1` 数据流，要把它变成可读可用的格式，你需要调用JavaScript的 `.json()` 函数来处理它。（还有一个类似的方法叫做`.text()`）

### 串联Promise

在使用 `.json()` 函数来处理fetch到的数据的body时，也需要一定的时间（特别是体量较大的数据），干等着肯定不是什么好办法。因此，JavaScript的 `.json()` 也是一个异步函数，它也会返回一个Promise。

当使用 `.then()` 时，我们可以返回另一个Promise，通过将 `.then()` 串联起来，我们可以对数据进行一连串的变换：

```js
function makeQuestion(dataString) { //a function to make a string a question
    return dataString + '???';
}

//image a hypothetical asynchronous function `getAsyncString`
//it returns a Promise (placeholder) for a string load from a given source
let originalPromise = getAsyncString(myDataSource);

//when the original promise is fulfilled, call `makeQuestion` on it
//`questionPromise` will be a placeholder for that transformed data
let questionPromise = originalPromise.then(makeQuestion);

//when the `questionPromise` is fulfilled, call an anonymous callback on it
//the callback will be passed the transformed ("question") data
questionPromise.then(function(data){
    console.log(data); //data will be a question!
})
```

使用匿名变量将上述代码写成串联形式：

```js
getAsyncString()
    .then(makeQuestion)
    .then(function(data){
        console.log(data);
    });
```

当我们成功获取数据后，即在调用 `.then()` 并随后在其的回调函数里返回一个新的Promise时，外层的Promise会自动获得返回的Promise的状态。这意味着你可以在前一个 `.then()` 里返回一个新的Promise，然后在后面串接的 `.then()` 里对这个新的Promise做出相应的操作：

```js
let outerPromise = getAsyncString(myFirstSource).then(function(firstData){
    //do something with `firstData`

    let newPromise = getAsyncString(mySecondSource); //a second async call!
    return newPromise; //return the promise.
}); //`outerPromise` now takes on the state and data of `newPromise`

outerPromise.then(function(secondData){
    //do something with `secondData`, the data downloaded from `mySecondSource`
});
```

回到刚才说的 `.json()` 函数，当使用 `fetch()` 获取第一个数据之后，我们可以在第一个 `then()` 的回调函数里让它返回另一个Promise，并在随后串联的 `.then()` 的回调函数里对数据进行进一步的操作：

```js
fetch(url)  //start the download
    .then(function(response) {  //when done downloading
        let dataPromise = response.json();  //start encoding into an object
        return dataPromise;  //hand this Promise up
    })
    .then(function(data) {  //when done encoding
        //do something with the data!!
        console.log(data); //will now be encoded as a JavaScript object!
    });
```

### 错误处理

当http请求失败的时候，我们使用 `.catch()` 来处理错误：This callback function will be passed an [Error object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error) that contains details about the error.

```js
fetch(url)
    .then(function(response) {  //when done downloading
        return response.json();  //second promise is anonymous
    })
    .then(function(data) {  //when done encoding
        //do something with the data!!
        console.log(data); //will now be encoded as a JavaScript object!
    })
    .catch(function(err) {
        //do something with the error
        console.error(err);  //e.g., show in the console
    });
```

- `.catch()` 对其之前串联的**所有Promise**发生的错误都起效，一般我们会在发生错误时提示用户，比如在DOM中弹出一个alert。
- 你可以在 `.catch()` 后再串联 `.then()` ，但这些后续的回调函数只会在不发生错误的时候被执行。

**注意**：Promise只有在<u>真正的错误</u>发生的时候才会被“reject”，如果服务器返回了诸如 [401](https://httpstatuses.com/401) 错误（对资源无访问权限）或只是一条“invalid API key”信息，`.catch()` 不会处理上述信息！

> You can use the `response.status` and `response.ok` properties to check the status of the HTTP response.
>
> 你可以使用  `response.status` and `response.ok` 属性来检查HTTP response。

## 其他数据格式

`.fetch()` 对 `json` 格式的数据非常有效，但你有时候需要处理不同格式的数据，比如 comma-separated values (CSV) 等， 以下是几种可以使用的方法：

- 对于plain text，你同样可以使用 `.fetch()` 来下载它们，只不过这回你调用 `text()` 函数来解析它们：

```js
fetch(url)  //start the download
    .then(function(response) {  //when done downloading
        let dataPromise = response.text();  //start encoding into a String
        return dataPromise;  //hand this Promise up
    })
    .then(function(text) {  //when done encoding
        //do something with the text data!!
        console.log(text); //will now be encoded as a JavaScript string!
    });
```

> `text()` 函数支持所有文本数据，不论是 `.txt` 还是 `.csv` 或 `.json` 和 `.js` 文件，它都支持！如果你使用 `.text()` 来编码 JSON数据，那么你可以使用 [`JSON.parse()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse)  来将其转换为JavaScript对象！

虽然 `.text()` 可以将收到的数据进行编码，使其成为JavaScript 的 String，但我们往往还需要将其转换为对我们有用的数据格式，比如，你可能需要将一段由 CSV 转换而来的字符转换到一个对象数组中来对这些数据进行分析。

与其辛苦自己写这些转换函数，你可以利用外部三方库来搞定这些杂活：

 比如 [**`d3.dsv()`**](https://github.com/d3/d3-fetch), [d3](https://d3js.org/) visualization library的一个组件. `d3.fetch()`  为 `fetch()` 提供了非常方便的Wrapper函数，能高效地解析收到的数据。

要引入 `d3.fetch()`，方法如下：

```html
<script src="https://d3js.org/d3-dsv.v1.min.js"></script>
<script src="https://d3js.org/d3-fetch.v1.min.js"></script>
```

