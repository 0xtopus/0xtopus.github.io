---
title: ES6 feature Note
date: 2023-11-27 15:41:33
tags:
---

# ES6+ 特性

2015年，ES6标准发布，增加了许多新特性。这里将对这些特性进行讨论，特别是那些被用于React框架里的特性。

> 注意，[大部分浏览器](http://kangax.github.io/compat-table/es6/)现在都支持ES6特性，但IE依然不支持，如果你需要兼容IE，你可以使用 [Babel](https://babeljs.io/) compiler来将代码“重新编译”为ES5代码。你可以在React框架里用 build tools自动完成重新编译。

## 类

ES6引入了新的 `class` 关键字，使你可以像Java那样面向对象编程。

- 注意，在JavaScript里，所有的类的实例都是 `Object` 类型的变量，尽管它表现得很像Java中的类，但还是有所不同。

### Why Class？

引入类的好处是：我们可以对一些数据（变量）和行为（方法）提供封装，以便在更高层面的抽象上来讨论它们。

在JavaScript中，我们可以通过定义 `Object` 类型的变量来定义一个实例：

```javascript
let person = {
   name: 'Ada',
   age: 21,
   costume: 'Cheshire Cat'
   trickOrTreat: function(newCandy){
      this.candy.push(newCandy);
   }
}

//tell me about this person!
console.log(person.name + " is a " + person.costume);
```

但类给我们提供了一个批量创建实例的模版，我们通过实例化类来创建同一类的不同对象。

### Java中的类

复习一下，在Java中定义类：

```java
//class declaration
public class Person {

    //attributes (private)
    private String firstName;
    private int age;

    //a Constructor method
    //this is called when the class is instantiated (with `new`)
    //and has the job of initializing the attributes
    public Person(String newName, int newAge){
        //assign parameters to the attributes
        this.firstName = newName;
        this.age = newAge;
    }

    //return this Person's name
    public String getName() {
        return this.firstName; //return own attribute
    }

    //grow a year
    public void haveBirthday() {
        this.age++; //increase this person's age (accessing own attribute)
    }

    //a method that takes in a Person type as a parameter
    public void sayHello(Person otherPerson) {
        //call method on parameter object for printing
        System.out.println("Hello, " + otherPerson.getName());

        //access own attribute for printing
        System.out.println("I am " + this.age +  " years old");
    }
}
```

在Java中使用类：

```java
public static void main(String[] args) {
    //instantiate two new People objects
    Person aliceObj = new Person("Alice", 21);
    Person bobObj = new Person("Bob", 42);

    //call method on Alice (changing her fields)
    aliceObj.haveBirthday();

    //call the method ON Alice, and PASS Bob as a param to it
    aliceObj.sayHello(bobObj);
}
```

这里对语法进行一个总结：

1. 使用 `class` 关键字对类进行声明；
2. Java的类中的变量（attributes）在类的定义顶部定义；
3. Java**使用构建函数**来初始化类的实例化对象里的变量；
4. 类的方法定义在类定义的内部，使用该类的实例调用类里定义的方法时可以使用：`obj.foo()`
5. 在类里调用类的方法和变量时可以用 `this.attr` 和 `this.foo()` 来调用；
6. 使用 `new` 关键字来实例化对象，实例化对象是一个变量，可以作为参数传递；

### ES6的类

定义如下：

```javascript
//class declaration
class Person {

    //a Constructor method
    //this is called when the class is instantiated (with `new`)
    //and has the job of initializing the attributes
    constructor(newName, newAge) {
        //assign parameters to the attributes
        this.firstName = newName;
        this.age = newAge;
    }

    //return this Person's name
    getName() {
        return this.firstName; //return own attribute
    }

    //grow a year
    haveBirthday() {
        this.age++; //increase this person's age (accessing own attribute)
    }

    //a method that takes in a Person type as a parameter
    sayHello(otherPerson) {
        //call method on parameter object for printing
        console.log("Hello, " + otherPerson.getName());

        //access own attribute for printing
        console.log("I am " + this.age +  " years old");
    }
}
```

用例如下：

```javascript
//instantiate two new People objects
let aliceObj = new Person("Alice", 21);
let bobObj = new Person("Bob", 42);

//call method on Alice (changing her attributes)
aliceObj.haveBirthday();

//call the method ON Alice, and PASS Bob as a param to it
aliceObj.sayHello(bobObj);
```

几点note：

1. JavaScript也使用 `class` 关键字来定义类；

   - 所有类变量名都需要使用PascalCase命名法！

2. JavaScript中定义类不需要事先声明变量，所有的变量在未被定义时都是 `undefined`;

3. JavaScript中的构造函数只有一个，名字就叫：`constructor()`；

4. 在JavaScript的类里定义方法的时候不需要使用 `function` 关键字；

5. 实例化对象的方式和Java是一样的，使用 `new` 关键字，比如：

   `let aliceObj = new Person("alice", 21);`

6. 和Java不同，你可以在一个文件里定义多个类：

```javascript
//script.js
'use strict';

//declare a class
class Dog {
    bark() { /*...*/ }
}

//declare another class
class Cat {
    meow() { /*...*/ }
}

//declare a (non-class) function
function petAnimal(animal) { /*...*/ }

//at the "main" level, instantiate the classes and call the functions
let fido = new Dog();
petAnimal(fido); //pass this Dog object to the function
```

### 继承

ES6也允许类的继承，就像Java一样，使用 `extend` 关键字来继承：

```js
//The "parent/super" class
class Dog {
  constructor(name) {
      this.name = name;
  }

  sit() {
      console.log('The dog '+this.name+' sits. Good boy.');
  }

  bark() {
      console.log('"Woof!"');
  }
}

//The "child/sub" class (inherits abilities from Dog)
class Husky extends Dog {
    constructor(name, distance) {
        super(name); //call parent constructor
        this.distance = distance;
    }

    //a new method ("special ability")
    throwFootball() {
        console.log('Husky '+this.name+' throws '+this.dist+' yards');
    }

    //override (replace) parent's method
    bark() {
      super.bark(); //call parent method
      console.log("(Go huskies!)");
    }
}

//usage
let dog = new Husky("Harry", 60); //make a Husky
dog.sit(); //call inherited method
dog.throwFootball(); //call own method
dog.bark(); //call own (overridden) method
```

继承允许一个子类继承父类的方法和在父类的基础上实现更多的专有功能，且允许你覆写（override）父类的方法。

> 但注意，JavaScript不是一门面向对象语言，而是使用了 [prototype system](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Object_prototypes) 来定义 `Object` 对象， 这个特性允许 [prototypical inheritance](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Inheritance) ，ES6的 `class` 只是提供了一种简洁的语法来实现指定对象的 prototypes罢了。这种特性从第一版的JavaScript里就存在了。
>
> See [this (detailed) explanation](http://aaditmshah.github.io/why-prototypal-inheritance-matters/) for further discussion.



## 箭头函数

ES6提供了一种快速简洁的匿名函数写法：lamda functions / arrow functions:

```js
let sayHello = (name) => {
    return 'Hello '+name;
}

// equal to:
let sayHello = function(name) {
  return 'Hello '+name;
}
```

几点tips：

- 如果箭头函数没有参数，那么必须在 `=>` 前面加上 `()`，如果箭头函数有两个及以上的参数，也必须加上 `()` 但是，如果仅有一个参数，你可以直接写成：`let sayHello = name => 'Hello '+name;` ；尽管如此，还是建议加上括号以方便阅读；
- 如果箭头函数只有一个语句，那么你可以省略 `{}` ；
- 如果箭头函数只有一个语句，且该语句return了一个值，那么你可以省略 `return` ；

- 但如果函数不返回值，还是建议使用 `{}` 将函数体包围起来；

```js
let array = [1,2,3]; //an array to work with

//normal function
array.map(function(num) {
  return num*2; //multiply each item by 2
});

//arrow syntax
array.map(num => {
  return num*2; //multiply each item by 2
});

//concise body
array.map(num => num*2);
```

箭头函数非常简洁，正在快速变成标准的JS匿名函数写法。

**尽可能使用箭头函数来表示匿名函数！！！**原因见[这里](#why-use-arrow-functions)。

## this关键字

如前所述，this可以用来指代当前的对象：

```js
let doggy = {
  name: "Fido",
  bark: function() {
      console.log(this.name, "woofs"); //`this` is object the function was called on
  }
}

doggy.bark(); //=> "Fido woofs"
```

但请注意，function是变量，所以可以被重新赋值；而this是在**执行时间（execution time）**里实时定义的，因此其代表了在运行代码时指向的对象：

```js
//An object representing a Dog
let doggy = {
  name: "Fido",
  bark: function() { console.log(this.name + " woofs"); }
}

// An object representing another Dog
let doggo = {
  name: "Spot",
  bark: function() { console.log(this.name + " yips")}
}

//This is Fido barking
doggy.bark( /*this = doggy*/ ); //=> "Fido woofs"

//This is Spot barking
doggo.bark( /*this = doggo*/ ); //=> "Spot yips"

//This is Fido using Spot's bark!
doggy.bark = doggo.bark; //assign the function value to `doggy`
doggy.bark( /*this = doggy*/) //=> "Fido yips"
```

- 注意 this 关键字指向的是调用方法的对象！

### 回调函数的问题

看下面这段代码：

```js
class Person {
   constructor(name){ this.name = name; } //basic constructor

   //greet each person in the given array
   greetAll(peopleArray) {
      //loop through each Person using a callback
      peopleArray.forEach(function(person) {
         console.log("Hi"+person.name+", I'm "+this.name);
      });
   }
}
```

由于 `forEach()` 的回调函数是在需要时才被调用，其被调用时并不是通过 **dot notation**来调用的，所以调用时里面的 `this.name` 就变成了 `undefined.name`，因此触发报错： `TypeError: Cannot read property 'name' of undefined`.

解决这个问题的方法就是**用箭头函数**来写匿名回调函数！

箭头函数有一个特性：和周围的代码共享语义上的 `this` 关键字。<span id="why-use-arrow-functions">这就是你为什么总是需要使用箭头函数来表示匿名回调函数的原因！</span>

```js
class Person {
   constructor(name){ this.name = name; }

   greetAll(peopleArray) {
      peopleArray.forEach((person) => { //arrow function (subtle difference)
         console.log("Hi"+person.name+", I'm "+this.name); //works correctly!
      });
   }
}
```

> 除此之外，你也可以用 `function.bind()` 来永久地把一个 `this` 和某个函数绑定，这样无论谁调用这个函数，它所代表的this永远不变：
>
> ```js
> //re-assign function
> myFunction = myFunction.bind(thisValue);
> ```
>
> `.bind()` 会返回一个新函数，上面是常用的用法：把新返回的绑定好this的函数赋值给旧函数。
>
> This is a common pattern in React (and has some minuscule performance benefits), 但是在这门课程中：you should stick with arrow functions for cleanliness and readability.

## Destructuring and Spreading

[**destructing assignments**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)：你可以destruct一个数组或对象，即将它们的值一次性赋给一组分立的变量：

```js
let myArray = [1, 2, 3];
let [x, y, z] = myArray; //assign myArray elements to `x`, `y`, `z` respectively
console.log(x); //=> 1;
console.log(y); //=> 2;
console.log(z); //=> 3;
```

```js
let myObject = {a: 1, b: 2, c: 3};
let {a, b, c} = myObject;
console.log(a); //=> 1
console.log(b); //=> 2;
console.log(c); //=> 3;
```

注意，当destruct一个对象的时候，如果等式左边的变量名和对象的 key 名字不同，那么就不会被赋给相应的值，而是为 `undefined` 。

如果你想要把值赋给不同名字的变量，见： There is syntax to assign values to different variable names as well, see [the documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Object_destructuring) for details.

### Spreading

如果等式左边的变量和数组或对象里的内容数量不同时，我们可以使用 `...` ( [**spread operator**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator) ) 来将溢出的值塞到一个数组里：

```js
let dimensions = [10, 20, 30, 40];
let [width, height, ...rest] = dimensions
console.log(width);  //=> 10
console.log(height); //=> 20
console.log(rest);   //=> [30, 40]; the rest of the values!
```

同样的，如果一个函数需要传入不定数目的变量时，也可以使用 `...` ：

```js
//a function that logs out all of the parameters
function printArgs(...args){
    //all the parameters will be grouped into a single array `args`
    args.forEach((arg) => {
        console.log(arg) //can log out all of them, no matter how many!
    });
}

printArgs('a', 'b', 'c'); //=> "a" "b" "c"
printArgs(1,2,3,4,5,6); //=> "1" "2" "3" "4" "5" "6"

//a function that adds up all the arguments (no matter how many!)
function sum(...numbers) {
    //numbers is an array, so we can `reduce()` it!
    let total = numbers.reduce((runningTotal, num) => {
        return runningTotal + num; //new total
    }, 0); //start at 0

    return total;

    //or as one line with a concise arrow function:
    return numbers.reduce((total, n) => total+n);
}

sum(3 ,4, 3); // => 10
sum(10, 20, 30, 40); // => 100
```



## 模块化

当代码变得复杂冗长的时候，再把所有的代码都放在一个 `.js` 文件里就不太合适了。

> Such large files become difficult to read and debug (“where *was* that function defied?”), can introduce problems with the shared global namespace (“did I already declare a `user` variable?”), and overall mixes code in a way that violates the *Separation of Concerns* principle.

把代码拆分为不同的 “Module” 使其各司其职是一个好办法。你不需要使用 `<script>` 标签来逐个引入它们，ES6提供了一系列功能来组织它们。

最经常的做法是使用外部工具，如[webpack](https://webpack.js.org/)之类的来加载modules。在这些情况下，您将使用 Node JavaScript 运行时提供的模块加载器。

**虽然不经常这么用**，但如果你需要在浏览器里面使用Modules，你需要在 `<script>` 标签里声明 `type="module"` attribute。

```html
<!-- load a script that can include other modules -->
<script type="module" src="path/to/script.js"></script>
```

### modules 语法

> 本节介绍网络应用程序中使用的 ES6 模块（ECMAScript 规范的一部分）。不过，Node.js 默认使用另一种模块加载系统 [*CommonJS*](https://nodejs.org/docs/latest/api/modules.html#modules_modules)。该系统使用内置方法 `require()` 加载模块（结果返回一个 "导出 "变量）。通过将值赋值给 `module.exports` 全局变量来导出模块。本课程将专门使用 ES6 模块，但最好还是了解一下另一种 *CommonJS* 方法。

#### Import

和Java非常类似，使用 `import` 关键字来引入：

```java
//Java example: import `Random` variable from `java.util` to use globally
import java.util.Random
```

```js
//JavaScript: import the `Random` variable from a `util.js` module
import { Random } from './util';
```

在 `{}` 里包含你要引入的变量名（记住，函数也是变量！），然后在 `from` 关键字后面写上modules所在的相对路径；

- 注意：Node-based System，比如React，允许你省略 `.js` 扩展名。
- 注意：如果你没有使用 `./` 之类的来指定路径，Node Module加载器会自动取 Load Path （e.g., in `node_modules/`）寻找对应的modules。有时加载外部JS库我们会这么做：

```js
//with jquery installed in `node_modules/`
//import the `$` and `jQuery` variables
import {$, jQuery} from 'jquery';
```

#### Export

想要让另一个文件访问某个module里的变量，你需要先Export它们：

```js
/*** my-module.js ***/
export let question = "Why'd the chicken cross the road?";
export let answer = "To get to the other side";
export function laugh() {
    console.log("hahaha");
}
```

上面的方法叫做 **“name export”**，后面还会介绍**“default export”**。只要某个变量被import了，那么它就是全局可用的。 (just as if it were defined in a previous `<script>` tag)

有好几种方法来import和export：

```js
/*** my-module.js ***/
export function foo() { return 'foo'; } //make available (as above)

function bar() { return 'bar'; }
export bar; //export previously defined variable

export { bar as barFunction }; //provide an "alias" (consumer name) for value

//will not be available (a "private" function)
function baz() { return 'baz'; }
```

```js
/*** index.js ***/
import {foo, barFunction} from './my-module'; //import multiple values
foo() //=> 'foo'
barFunction() //=> 'bar'

import {foo as myFoo} from './my-module'; //provide an "alias" for value
myFoo(); //=> 'foo'

import * as theModule from './my-module'; //import everything that was exported
                                          //loads as a single object with values
                                          //as properties
theModule.foo(); //=> 'foo'
theModule.barFunction(); //=> 'bar'
theModule.baz(); //Error [private function]
```

通过 `as` 关键字，你可以指定某个变量的“假名”，so it is loaded and assigned a different name。这使得你可以将模块里复杂的名字重命名为更简单的名字。

你也可以import everything：使用 `import * as` 语法，你可以指定一个代表导出Module的对象名称（如示例中的`theModule`），每个导出变量都将是该对象的属性。**当你在开发过程中可能要向模块添加额外的导出值，但又不想不断调整导入语句时，这种方法特别有用。**



以及，“default export” 允许每个模块只导出一个默认的变量，为引入模块提供了一些简化。

```js
/*** my-module.js ***/
//this function is the "default" export
export default function sayHello() {
    return 'Hello world!';
}
```

在 `export` 后使用 `default` 关键字，这样就可以在引入时只需提供假名来直接引入默认变量。

```js
/*** index.js ***/
import greet from './my-module'; //import the default value

greet(); //=> "Hello world!"
```

匿名变量也可以作为default输出：

```js
/*** animals.js ***/
export default ['lion', 'tiger', 'bear']; //export anonymous array
```

默认导出技术在 React 等面向对象框架中尤为常见，在这些框架中，您可以让每个 JavaScript 模块都包含一个函数或类的代码。可以将单个类作为默认导出，这样其他模块就可以快速轻松地导入它，如

```js
import MyComponent from './MyComponent.js'
```

## 其他特性

> Syntactic Sugar causes cancer of the semicolon - [Alan Perlis](http://www.cs.yale.edu/homes/perlis-alan/quotes.html)

### Template Strings

ES6允许你在字符串里直接插入表达式：使用后勾号（\```）而不是引号来标注，并将注入的表达式写在 `${}` 标记内：

```js
let name = 'world';
let greeting = `Hello, ${name}!`; //template string
console.log(greeting); //=> "Hello, world!"
```

然而在字符串里放太多的表达式会影响程序的可读性，所以：

```js
let name = 'world';

//greeting with capitalization. Don't do this!
let greeting = `Hello, ${name.substr(0,1).toUpperCase() + name.substr(1)}!`
console.log(greeting); //=> "Hello, world!";

//do this instead!
let capitalizedName = name.substr(0,1).toUpperCase() + name.substr(1);
let greeting = `Hello, ${capitalizedName}`
console.log(greeting); //=> "Hello, world!";
```

比较常见且可能有用的 ES6 特性，其中大部分只是 "语法糖"，使用 ES5 风格的 JavaScript 也能实现类似的行为和功能。因此，您不需要在代码中使用这些功能（尽管它们可能会有所帮助），它们通常会在我们使用 React 等库的过程中出现。

