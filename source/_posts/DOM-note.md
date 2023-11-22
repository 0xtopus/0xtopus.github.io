---
title: DOM note
date: 2023-11-22 19:32:24
tags:
---
# DOM

参考：[Document Object Module(DOM)](https://info340.github.io/dom.html)

DOM是Document of module的缩写。当浏览器渲染 `html` 时，我们往往把其看成树状的结构，没有父节点的html元素被成为根元素，有父元素也有子元素的被称为node，没有子元素的被成为leaf。这种模型就叫做DOM。

JavaScript则可以用来操纵和修改DOM里的元素，DOM提供了一些API使我们可以很方便的对页面中的 html 元素进行操纵。

## 全局变量

JavaScript的全局变量几乎都是 `Object` 类型，它们的值是一组方法。比如：`Math` ， `console` ，`window` 等等：

比如，全局变量 `window` 代表浏览器自身：

```js
/* example properties */
let width = window.innerWidth;   //viewport width
let height = window.innerHeight; //viewport height
var url = window.location.href; //url for this page

/* example functions */
window.alert("Boo!"); //show a popup alert. Do not use this.
window.scrollTo(0, 1000); //scroll to a position
window.setTimeout(callback, 1000); //execute callback after an delay
window.setInterval(callback, 1000); //execute callback repeatedly after interval
```



> While these examples are included for completeness, most `window` functions are rarely used and should be avoided. Popups with the `window.alert()` function are inelegant, interrupt the user’s actions, and produce a bad user experience—you should instead use in-window alerting options instead (such as showing a `<p class="alert">`). Browser control functions such as `scrollTo()` are non-standard and can vary drastically across systems and platforms. Proceed with caution when using `window` functions!



### document

`document` 是DOM本身的全局变量，即当前浏览器页面中渲染的全部 html ，通过 `document` 对象来操纵当前页面的元素。

## 引用html元素

为了操纵html文件，你需要先引用它们。

`document` 对象里有许多“选择函数” 使你可以轻松引用到你想要的元素：

```js
//element with id="foo"
let fooElem = document.getElementById('foo');

//elements with class="row"
let rowElems = document.getElementsByClassName('row'); //note the plural!

//<li> elements
let liElems = document.getElementsByTagName('li'); //note the plural!

/*easiest to select by reusing CSS selectors! */
let cssSelector = 'header p, .title > p';  //a string of a CSS selector

//selects FIRST element that matches css selector
let elem = document.querySelector(cssSelector);

//matches ALL elements that match css selector
let elems = document.querySelectorAll(cssSelector);
```

- `querySelector` 是最常用的选择函数，你可以在括号里填 `id` ，`class` 或 `元素选择器` 来选出需要的元素。
- 注意：所有返回多个元素的选择函数返回的是一个  [`NodeList`](https://developer.mozilla.org/en-US/docs/Web/API/NodeList)，尽管它很像数组，但是 `forEach()` 和 `map()` 等方法对其无效！你必须使用 `for()` 循环来遍历它的元素！
- 一般而言，你一次只会对一个元素进行修改，所以不需要太担心上一点。



## 修改HTML元素

一旦你获得了html元素的引用，你就可以使用对象里对应的方法来对其进行修改。注意，修改后的元素不会影响到html源文件，刷新页面后，你的修改就会被覆盖（除非你同时也加载了对应的 `script` 文件）

### 修改元素的内容

使用 `elem.textContent` 来修改字面内容，这是一种非常安全的做法，即使有人试图引入 `<` 和 `>` ，它们也会被翻译为[HTML Entities](https://www.w3schools.com/html/html_entities.asp)而不是标签。

使用`elem.innerHTML` 则可以插入带有HTML标签的文字，注意，除非你能100%确定输入的内容是可靠的，否则请小心使用这个属性。

```js
//get a reference to the FIRST <p> element
let elem = document.querySelector('p');

console.log(elem); //to demonstrate

let text = elem.textContent; //the text content of the elem
elem.textContent = "This is different content!"; //change the content

let html = elem.innerHTML; //content including HTML
elem.innerHTML = "This is <em>different</em> content!"; //interpreted as HTML
```



### 修改元素的属性

你也可以改变某个元素的属性，在HTML Specification里定义的每个属性（attribute）都作为元素的property暴露出来。

```js
//get a reference to the `#picture` element
let imgElem = document.querySelector('#picture');

//access the attribute
console.log( imgElem.src ); //logs the source of the image

//modify the attribute
imgElem.src = 'my-picture.png';
```

但是，你不可以使用`elem.class` 和 `elem.style`来访问某个属性的类名和CSS。

使用 `getAttribute()` 方法和 `setAttribute()` 方法可以直接对某个属性进行修改，注意给出对应的参数：

```js
let imgElem = document.querySelector('#picture');
imgElement.setAttribute('src', 'my-other-picture.png'); //set the src

console.log( imgElem.getAttribute('src') ); //=> 'my-other-picture.png'

//the `hasAttribute()` method returns a boolean.
let isThick = document.querySelector('svg rect')
                      .hasAttribute('stroke-width'); //chained anonymous variables
```

你可以用上述方法来修改未被HTML Spec定义的属性，比如说`data-` attribute。但它们对一些属性也不起作用，比如： `value` attribute of an `<input>` element。

### 修改元素的类名和CSS

使用 `classList` 属性来修改类名。除了IE以外，可以使用 `add()` 和 `remove()` 方法来增加和减少类名：

```js
//access list of classes of the element 'elem'
let classList = elem.classList;

//add a class
elem.classList.add('small'); //add a single class
elem.classList.add('alert','alert-warning'); //add multiples classes (not on IE)

//remove a class
elem.classList.remove('small');

//"toggle" (add if missing, remove if present)
elem.classList.toggle('small');
```

- While IE 10+ does support these methods, it doesn’t support *multiple arguments* for them (so you can’t add multiple classes in a single method call). If you need to support older browsers (including any version of IE), you can instead modify the `.className` property as if it were a String:

  ```js
  //fallback for IE (all)
  var classes = elem.className;
  classes += ' '+ 'sweet sour'; //modify the string (append!)
  elem.className = classes;     //reassign
  ```

  The `classList` methods work perfectly on Microsoft Edge.

你也可以直接修改 `style` 属性，但一般不推荐这么改：

```js
let h1 = document.querySelector('h1');
h1.style.color = 'green';
h1.style.backgroundColor = 'black'; //not `.background-color`
```

### 修改DOM Tree

你也可以操纵DOM Tree，即增加或删减、修改页面里的元素。

JavaScript的DOM元素可以使用特殊的变量来引用它们的子元素和父元素，这些变量具有只读属性。

```html
<main>
    <section id="first-section">
        <p>First paragraph</p>
        <p>Second paragraph</p>
    </section>
    <section id="second-section"></section>
<main>
```

```js
//get reference to the first section
let firstSection = document.querySelector('#first-section');

//get reference to the "parent" node
let main = firstSection.parentElement;
console.log(main); //<main>...</main>

//get reference to the child elements (2 paragraphs)
let paragraphs = firstSection.children;
console.log(paragraphs.length); //2
console.log(paragraphs[0]); //<p>First paragraph</p>

//get reference to the the next sibling
let sectionSection = firstSection.nextElementSibling;
console.log(secondSection); //<section id="second-section"></section>
```

- Note that these properties only deal with **HTML elements**—text content nodes are ignored. You can instead use equivalent properties `parentNode` and `childNodes` to also consider text content nodes.

即除了元素节点之外，DOM里面还可以有：“A node can be an element node, a text node, a comment  node, or a document node.”

以及：SVG content doesn’t support `parentElement`, but does support `parentNode`.



你可以使用 `Document.createElement()` 来创建新的元素，然后使用 `appendChild()` 等方法来将新创建的元素插入DOM：

```js
//create a new <p> (not yet in the tree)
let newP = document.createElement('p');
newP.textContent = "I'm new!";

//create Node of textContent only (not an HTML element, just text)
let newText = document.createTextNode("I'm blank");

let main = document.querySelector('main');
main.appendChild(newP); //add element INSIDE (at end)
main.appendChild(newText); //add the text inside, AFTER the <p>

//add anonymous new node BEFORE element. Parameters are: (new, old)
main.insertBefore(document.createTextNode("First!"), newP);

//replace node. Parameters are: (new, old)
main.replaceChild(document.createTextNode('boo'), newText);

//remove node
main.removeChild(main.querySelector('p'));
```

常见的做法是使用 `document.createElement()` 先创建一个块元素，然后设置这个块元素的 `innerHTML`，在里面添加文字内容和行内元素，最后使用 `appendChild()` 把这个元素放到DOM的某个位置中去。

### Accessibility

如果在阅读器已经阅读后对DOM的内容进行了修改，那么 Accessibility 就会受到影响。将将来可能被修改的元素放到[ARIA Live Region](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Live_Regions)，阅读器会根据修改进行调整。

You make an element into a live region by giving it the `aria-live` attribute:

```html
<div aria-live="polite">
  This content can change!
</div>
```

`aria-live` 的属性 `"polite"` 表明只有当用户暂停当前正在阅读的内容的时候才把修改的内容读出。

> A `"polite"` alert doesn’t interrupt the currently being read text or description, but instead will be injected when there is a break (if the current reading goes on for too long, then the new content will not be spoken).

> The other option is `"assertive"`, which indicates that the new content should be spoken as soon as it changes, possibly interrupting other content. This should only be used for important information (like alerts, warnings, or errors), as it can interrupt the user’s flow in ways that are very disorienting. In short: *always be polite!*

另外你也可以使用 `"assertive"` 属性，这表示一旦发生改变，改变的内容会被立刻读出。

## 事件

当用户和电脑互动的时候，操作系统会把这一系列互动当做 “事件（Event）”，比如按键被按下，鼠标点击等等。

为了使我们的JavaScript能够对事件进行响应，我们需要注册“Event Listener” 来监听事件。在DOM API中，我们使用函数 `addEventListener()` 来给某个元素添加事件监听。

该函数有两个参数：

- 预先定义的事件名称
- 事件发生时调用的回调函数，该函数有一个类型是 Object 的参数 `event` ，里边包含了事件发生的一些信息

```js
//a (named) callback function
function onClickCallback() {
    console.log("You clicked me!");
}

//get a reference to the element we want to "listen" to
let button = document.querySelector('button');

//register a listener for 'click' events
button.addEventListener('click', onClickCallback);
```

一般我们使用匿名函数：

```js
let button = document.querySelect.select('button');
button.addEventListener('click', function(event) {
    console.log("You clicked me!");
});
```

可以给同一个元素绑定多个不同的事件监听器。

回调函数的参数 `event` 含有事件发生时的信息，比如事件发生时鼠标的位置，哪个元素触发了事件等等：

```js
elem.addEventListener('click', function(event) {
   //get who was clicked;
   let clickedElem = event.target; //target property of the event
   console.log(clickedElem);
});
```

如果你想要覆盖掉某事件发生时系统默认的行为，比如取消在按下按钮时默认提交表格：

```js
submitBtn.addEventListener('click', function(event) {

  event.preventDefault(); //don't do normal behavior
  event.stopPropagation(); //don't pass the event to parents

  //..do custom behavior here

  return false; //don't do normal behavior OR propagate! (for IE)
})
```

### 事件的类型

There are [numerous different events](https://developer.mozilla.org/en-US/docs/Web/Events) that you can listen for, including:

- [Mouse Events](https://developer.mozilla.org/en-US/docs/Web/API/MouseEvent) 比如 **`'click'`**。 

  -  `event.offsetX` 和 `event.offsetY` will provide (x,y) coordinates for the clicks location *relative to the target element*; 
  -  `clientX/Y` for coordinates relative to the browser window
  -  `pageX/Y` for coordinates relative to the document (regardless of scrolling). 
  - See [this post](http://www.quirksmode.org/mobile/viewports.html) for details, and [this page](http://www.quirksmode.org/m/tests/mouseprops.html) for an example.

  其他的鼠标事件还包括： `'dblclick'` (double-click), `'mousedown'` (mouse button is pressed down, may be held), `'hover'` (mouse hover), `'mouseenter'` (mouse moves over element), `'mousemove'` (mouse moves over element), and `'mouseleave'` (mouse moves off element).





- [Keyboard Events](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent) 比如 **`'keydown'`**。
  - `event.key` 属性用来判断按下的是哪个键, giving a [predefined key value](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/key/Key_Values) you can check:

```js
elem.addEventListener('keydown', function(event){
    if(event.key === 'ArrowUp'){
        console.log("Going up!")
    }
    //...
});
```

> The `event` object also has properties to check if any “modifier keys” such as shift, control, or meta (Windows/command) are held when the event occurs.
>
> Note that you almost always want to respond to the `'keydown'` and `'keyup'` events; the `'keypressed'` event is sent later and only applies to non-modifier keys.





- [Window Events](https://developer.mozilla.org/en-US/docs/Web/API/Window#Event_handlers) 是 `window` 全局变量的事件。 
  - 比如， `'resize'` 事件可以用来确定浏览器窗口是否发生了大小改变 (e.g., if you want to make the content responsive as well as the CSS):

```js
window.addEventListener("resize", function() {
    //...
});
```

(See [the documentation](https://developer.mozilla.org/en-US/docs/Web/Events/resize) for advise on using this callback)

- `window` 全局变量还定义了一个事件回调函数 `window.onload` ，允许你在页面加载完毕后再运行此回调函数内的代码(e.g., for scripts specified in the `<head>`)

  ```js
  window.onload = function() {
      //...do stuff once page is ready (e.g., run the rest of your code)
  }
  ```



**注意：** 永远不要在HTML里使用 `onclick` 属性！为了与解耦的原则保持一致，永远应该在JavaScript里添加Event Listener！



### 事件驱动编程

在事件监听器中定义的回调函数里的变量的作用域只在函数内有效，这就意味着有时我们需要使用全局变量来记录事件的一些属性，比如事件发生的次数：

```js
let clickCount = 0;  //keep track of the "state" (global)
document.querySelector('button').addEventListener('click', function() {
    if(clickCount > 10) {  //decide what to do
        console.log("I think you've had enough");
    }
    else {
        clickCount++;  //change state (+1)
        console.log('You clicked me!');
    }
});
```

> These “state” variables can be global, or can simply be declared within a containing function as a closure. State variables are often objects, with individual values stored as the properties. This provides a name-spacing feature, and helps to keep the code from being cluttered with many variables.

