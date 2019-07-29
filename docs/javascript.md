# JavaScript 篇

## JS 的基本类型有哪些，引用类型有哪些？

六种原始类型（基本类型）：Undefined，Null，Boolean，Number，String，Symbol（ES6 新增）

引用类型：Object，Array，Date，RegExp，Function，基本包装类型（Boolean，Number，String 这是三种特殊的引用类型，也可以算作基本类型）

需要注意的是，`typeof` 的返回值（number, boolean, string, object, function, undefined, symbol）和 JS 的基本类型不一样。

## 哪些类型是存在于栈上的，哪些是存在于堆上的？

基本类型保存在栈上。

![](../assets/javascript-basic-type.png)

引用类型的引用保存在栈上，其对象保存在堆中。

![](../assets/javascript-reference-type.png)

## null 和 undefined 的区别

其实`null`和`undefined`从本质上讲，没什么太大的区别。如果硬要说区别的话，大概可以分为以下几点：

1. 含义不同：`null` 表示该处不应该有值，而`undefined`表示该处应该有一个值，但还没初始化。
2. 转换数值不同：`null = 0`，`undefined = NaN`

## JS 的继承有哪几种，分别有什么特点。

在面向对象语言中，继承分为两种：

- 接口继承：只继承方法签名
- 实现继承：继承实际的方法

因为 JavaScript 没有继承关键字，所以 JavaScript 实现继承的方法很特殊，大致有以下几种：

- 原型链继承（最常见）

```javascript
// 原型链继承
var Base = function() {
  this.name = 'Base'
  this.toString = function() {
    return this.name
  }
}
var Sub = function() {}
Sub.prototype = new Base()
Sub.name = 'Sub'
```

原型链继承是一种非常传统的继承方式。如果从`instanceof`关键字来看，实例既是父类的实例，也是子类的实例。但是这样的继承也有一个明显的问题：子类区别于父类的属性和方法需要在完成原型链继承之后再进行。同时，由于使用了 `prototype` ，无法实现多重继承。

补充一下：ES6 实现的`extends`也是原型链继承，但是实现方式略有不同（可以参考下面的问题「ES5 与 ES6 的继承有什么区别？」）。

- 构造继承

```javascript
var Base = function() {
  this.name = 'Base'
  this.toString = function() {
    return this.name
  }
}
var Sub = function() {
  Base.call(this)
  this.name = 'Sub'
}
```

这种继承解决了原型链继承的问题，但它也有一些问题：使用 `instanceof` 运算符的时候会发现，它的实例并不是父类的实例（因为父类没有在它的原型链上）。

- 实例继承

```javascript
var Base = function() {
  this.name = 'Base'
  this.toString = function() {
    return this.name
  }
}
var Sub = function() {
  var instance = new Base()
  instance.name = 'sub'
  return instance
}
```

这种继承方法只能强行被归类为继承。因为它实际上是返回了一个父类的实例，与子类可以说毫无关系。同样，这种方法也不支持多继承。

- 拷贝继承

```javascript
var Base = function() {
  this.name = 'Base'
  this.toString = function() {
    return this.name
  }
}
var Sub = function() {
  var base = new Base()
  for (var i in base) {
    Sub.prototype[i] = base[i]
  }
  Sub.prototype['name'] = 'Sub'
}
```

首先，只有可枚举的对象类型才能使用`foreach`的方式获取到，其次，这种写法真的快绝迹了。它的优点是可以实现多继承，缺点是写起来真的难受，而且效率很低。

## 什么是原型链？原型与原型链的区别是什么？

### 什么是原型链？

原型链是 JavaScript 实现继承最重要的一种方式。每一个对象都有自己的原型对象，根原型对象没有原型，所以其 `__proto__` 属性值为 `null` 。在调用时，如果访问的对象属性没有找到，JavaScript 会顺着原型链继续往下找，直到触碰到根原型为止。

### 原型与原型链的区别是什么？

这个问题我不太明白问什么，我猜测是在问 `__proto__` 和 `prototype` 的区别。  
`__proto__` 是隐式原型，指向创建这个对象的函数（constructor）的 `prototype`，用来构成原型链和实现基于原型的继承。  
`prototype` 是显式原型，用来实现基于原型的继承与属性的共享。每一个函数都具有 `prototype` 属性。（因为函数在 ES5 中具备 constructor 的功能）

## ES5 与 ES6 的继承有什么区别？

先上一张图：

![](../assets/es5-inherit.png)

![](../assets/es6-inherit.png)

区别显而易见：ES5 中子类继承的是父类的实例，ES6 中子类直接继承于父类。

## 原生 Ajax 的实现步骤

实现 Ajax ，除了正常的生成和发送，主要是利用 `onreadystatechange` 回调函数对 Ajax 进行控制。无论是使用 `XMLHttpRequest` 还是 `ActiveXObject('microsoft.xmlhttp')` ，实现 Ajax 都要掌握它的五个关键状态，即 `readyState` 可能的五个值：

![](../assets/xhr.readystate%20%281%29.png)

这是我给出的一个 DEMO：[https://github.com/lcx960324/fe-interview-cheat-sheet-demo/tree/master/JavaScript/Ajax](https://github.com/lcx960324/fe-interview-cheat-sheet-demo/tree/master/JavaScript/Ajax)

- **什么是闭包，什么时候（需要）构成闭包？**

闭包几乎是面试必考题了。闭包是指那些可以访问独立数据的函数，举个 MDN 的例子：

```javascript
function init() {
  let name = 'Mozilla'
  // name 是一个被init创建的局部变量
  function displayName() {
    // displayName() 是一个内部函数,
    alert(name)
    //  一个闭包使用在父函数中声明的变量
  }
  displayName()
}
```

`displayName` 就是一个典型的闭包，它能够访问它之外的 `name` 变量，并保存这个变量在它创建时的状态。这个特性非常实用，因为 JavaScript 有很多奇怪的坑，比如 this context，比如作用域问题。举两个闭包的典型例子：

```javascript
function createFunctions() {
    var result = new Array();
    for( var i = 0; i < 10; i++) {
        result[i] = function() {
            return i;
        };
    }
    return result;
} // 这个函数返回的 i 全是 10 ，因为 i 是活动变量
function createFunctions() {
    var result = new Array();
    for( var i = 0; i < 10; i++) {
        result[i] = function(num) {
            return function() {
            return num;
        }(i);
    }
    return result;
} // 这样就好了
```

```javascript
var name = 'The Window'
var object = {
  name: 'My Object',
  getNameFunc: function() {
    var _this = this
    return function() {
      return _this.name
    }
  }
}
alert(object.getNameFunc()()) // "My Object"
```

更多的内容，请参考：

- 通过闭包实现一个 throttle：[http://www.alloyteam.com/2012/11/javascript-throttle/](http://www.alloyteam.com/2012/11/javascript-throttle/)
- 一道经典的闭包面试题：[https://zhuanlan.zhihu.com/p/25855075](https://zhuanlan.zhihu.com/p/25855075)
- 阮一峰的博客：[http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html](http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html)
- 闭包的真正意义：[https://www.zhihu.com/question/34510484](https://www.zhihu.com/question/34510484)

## 如何禁止浏览器的默认事件？什么是事件冒泡？怎样防止事件冒泡？（CSS 也有一种方法）

- 如何禁止浏览器的默认事件？

  `event.preventDefault()`

- 什么是事件冒泡？

  事件冒泡指的是在某一节点上触发事件后，事件会沿着节点的父链逐级向上传递，直到根节点。

- 如何阻止事件冒泡？

  `event.stopPropagation()`

## readyState 的值分别代表什么状态。当 readyState === 3 的时候拔掉网线会怎么样？

readyState 来源于 `XMLHttpRequest`，所以翻翻文档就好了：

![](../assets/xhr.readystate.png)

至于第二个问题，我还真试了一下：  
状态会停留在 3 一段时间，然后因为超时跳转到 4 。

## 闭包的缺陷

由于闭包会携带包含它的函数作用域，所以它会比其他函数占用更多的内存，这可能会导致性能问题。所以在函数执行完后，应当尽量销毁闭包用不到的作用域中的变量，帮助系统回收内存。  
同时，在 IE9 一下的浏览器中，如果闭包的作用域链中保存着一个 HTML 元素，那么就意味着该元素无法被销毁，这可能会造成内存泄漏。  
还有，闭包可以访问到其外部的变量，所以要注意，如果将外部作用域当做一个对象来使用的话，如果闭包使用了内部变量，应当尽量避免修改闭包依赖的内部变量，因为这可能对闭包的行为造成影响。

## 编写一个简单的递归函数

送分题。最简单的：fibonacci 数列

```javascript
function fibonacci(n) {
  if (n === 1 || n === 2) {
    return 1
  }
  return fibonacci(n - 1) + fibonacci(n - 2)
}
```

## 说出 Event 对象的 3 - 5 个属性或方法

至少要知道的两个方法和一个属性：`event.preventDefault()`，`event.stopPropagation()`，`event.target`

至于其他的...：[https://developer.mozilla.org/en-US/docs/Web/API/Event](https://developer.mozilla.org/en-US/docs/Web/API/Event)

## 如何改变函数的 this 的指向？call, apply, bind 的区别？

使用 call apply bind 都可以改变函数的 this 指向。

`bind` 返回一个新的函数。

`call`和 `apply`都会直接执行函数，不同的是，`call` 直接接收函数参数，`apply` 需要把参数变成数组一并传入。

这三个函数的具体用法，可以查一下文档，很简单~

## JS 如何实现数组的浅拷贝和深拷贝？

[https://segmentfault.com/a/1190000008637489](https://segmentfault.com/a/1190000008637489)

先解释一下什么是深拷贝，什么是浅拷贝：

浅拷贝：拷贝对象的引用。

深拷贝：拷贝对象的实例。

数组的浅拷贝其实蛮简单的，有很多方式。比如 `Arrary.prototype.slice`。

深拷贝有黑科技：用 `JSON.parse` 和 `JSON.stringify` 超级好用！

以下是一个简单对象深拷贝的例子：

```javascript
function deepCopy(source) {
  if (typeof source !== 'object') {
    return source
  }
  const copy = {}
  for (let key in source) {
    if (typeof key === 'object') {
      copy[key] = deepCopy(source[key])
    } else {
      copy[key] = source[key]
    }
  }
  return copy
}
```

## 如何将数组转换为字符串？

我能想到的办法：

- `Array.prototype.reduce`

```javascript
const arr = []
arr.reduce((sum, val) => {
  return sum + ',' + val
})
```

- `Array.prototype.join`

```javascript
const arr = []
arr.join(',')
```

- `Array.prototype.toString`：用逗号分隔，效果和上边的一样。

## 如何将字符串转换为整数？

首先，介绍一个把字符串转换为数字的快捷方式（使用了 JavaScript 的自动类型转换）：

```javascript
const str = '123.45' // string
const num = +str // number
```

跑题了！题目要求把字符串转换为**整数**：

```text
const str = '123.45'
const num = parseInt(str)
const numES6 = Number.parseInt(str)
```

ES6 将 `parseInt` 移植到了 `Number` 类上，当然，你一样可以在全局使用这个方法。

## parseInt 的第二个参数代表什么？

先看看文档怎么说：![](../assets/parseint.png)是不是没看懂（或者懒得看...）！其实就是告诉 `parseInt`你传入的数字字符串的是几进制。

## 完整的 map, reduce, filter 都包含哪些参数

- `map` 接收两个参数：
  - `callback`：`map` 的回调函数，接收三个参数：
    - `currentValue`：当前从数组取出的元素
    - `index`：该元素在数组中的位置
    - `array`：原始数组
  - `thisArg`（可选）：执行 `callback`时的`this`上下文。
- `reduce` 接收两个参数：`reduce` 的回调函数，接收四个参数：
  - `callback`：
    - `accumulator`：上一个 reduce 函数的返回值（对于第一次执行，如果指定了 `initialValue`，会是 `initialValue` 的值，如果没有，默认使用原始数组的第一个元素值）
    - `currentValue`：当前元素
    - `currentIndex`：该元素在数组中的位置
    - `array`：原始数组
  - `initialValue`（可选）：初始值。
- `filter`：同`map`，不过 `callback`返回的是 `true` 或者 `false`。

## 简述一下 prototype

`prototype` 是原型，是 JS 实现面向对象特性的重要组成部分。在 ES6 之前，JavaScript 是没有类的概念的，实现类的方法通常是借助函数。每一个函数都是一个对象，且都具有 `prototype` 属性，这个属性定义了函数的原型。当使用该函数生成实例的时候，该实例会自动继承这个原型。JavaScript 具有原型链调用的特性，当我们访问某一个属性时，如果该属性在这个对象上找不到，会沿着这个对象的原型链一直寻找，直到找到或达到原型链底部为止。所以 `prototype` 是实现 JS 类与类的继承的重要元素。

## 事件冒泡与 Ajax 的兼容性问题

### 事件冒泡的兼容性问题

说实话，事件冒泡我没想到什么兼容性问题。倒是事件有兼容性问题。事件的兼容性问题主要源自于 IE 与 Webkit 内核实现 Event Handler 的方式不同。红宝书的第 13 章提到了大量关于 IE 事件特殊性的地方。  
首先，我们习惯使用的 `addEventListener` 和 `removeEventListener` 在 IE 中被实现为 `attachEvent` 和 `detachEvent` 。IE9 开始支持标准的 `addEventListener` 和 `removeEventListener` ，IE11 开始正式废弃了 `attachEvent` 和 `detachEvent` 。  
同时 IE 实现阻止默认事件和阻止事件冒泡的方式也不一样。IE 的 event 对象有两个属性：`cancelBubble` 和 `returnValue` ，将这两个属性设置为 `false` 可分别组织事件冒泡和默认行为。  
还有就是 IE8 之前的 `Event` 对象并没有提供 `target` 属性。  
顺便提一下，不是所有的 DOM 事件都会冒泡，比如 `blur` 事件和 `focus` 事件就是默认不冒泡的。

### Ajax 的兼容性问题

这个问题我也不是很清楚，我唯一能想到的就是 `XMLHttpRequest` 和 `fetch` 的问题。`XHR1` 是从 IE7 开始提供支持的，IE10 开始基本完整的支持 `XHR`。 `fetch` 使用了 `Promise`，目前所有的 IE 版本都不支持这个函数。  
同时提一下，IE8 通过 `XDomainRequest` 支持了 CORS，IE10 开始完全支持了 CORS。（CORS：Cross-Origin Resource Sharing）  
以下是 `XHR` 兼容性的详细信息（注意那些 IE 没有实现的方法）：

![](../assets/xhr.png)

在老版本的 IE 中，可以使用 `req = new ActiveXObject('Microsoft.XMLHTTP')` 生成 Ajax 对象代替 `XHR` 。  
当然，我们还需要一种 polyfill 彻底解决这个问题，欢迎扩充。

## 给定一个 DOM 元素，获取它相对于视图窗口的坐标

IE4 最先提供了获取视口坐标的函数：`Element.getBoundingClientRect()`，随后，Webkit 也提供了支持。这个函数返回一个 `DOMRect` 对象，该对象的 `x`，`y` 即是当前元素的左上角相对于视口左上角的坐标。  
需要注意的是，IE8 及之前版本的 `DOMRect` 没有 `height` 和 `width` 属性。  
详细信息可以参考文档：  
getBoundingClientRect：[https://developer.mozilla.org/zh-CN/docs/Web/API/Element/getBoundingClientRect](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/getBoundingClientRect)  
DOMRect：[https://developer.mozilla.org/zh-CN/docs/Mozilla/Tech/XPCOM/Reference/Interface/nsIDOMClientRect](https://developer.mozilla.org/zh-CN/docs/Mozilla/Tech/XPCOM/Reference/Interface/nsIDOMClientRect)

## offsetHeight， scrollHeight, clientHeight 分别代表什么？

- `offsetHeight`：元素在垂直方向上占用的空间大小，以像素计。（高度 + padding + 滚动条占用的高度 + 边框）
- `scrollHeight`：在没有滚动条的情况下，元素内容的总高度。（完全展开的高度 + padding）
- `clientHeight`：元素内容区高度加上上下内边距高度。（高度 + padding）

## JavaScript 严格模式

阮一峰老师的博客对严格模式给出了非常好的解读：[http://www.ruanyifeng.com/blog/2013/01/javascript_strict_mode.html](http://www.ruanyifeng.com/blog/2013/01/javascript_strict_mode.html)  
我总结为以下几点：

- 全局变量要显式声明
- 不允许使用 `with`
- 为 `eval` 添加了独立的作用域
- `this === undefined` 时不会默认指向全局对象
- 禁止在函数内部遍历调用栈（`caller` 和 `arguments`）
- 无法删除变量（不能使用类似 `delete x` 这样的语法）
- 只有 `configurable` 设置为 `true` 的对象的属性，才能够被删除。
- 正常模式下不会报错的失败操作，在严格模式下会报错，比如对 `getter` 赋值，修改只读属性等。
- 对象不允许出现重名属性
- 函数不能出现重名参数
- 禁止八进制表示法
- 对 `arguments` 的限制：

  - 不允许对 `arguments` 赋值
  - `arguments` 不再追踪参数变化，即在函数内修改参数值不会影响到 `arguments` 。

    ```javascript
    function f(a) {
      'use strict'
      a = 2
      return [a, arguments[0]]
    }
    f(1) // [2, 1]
    ```

  - 禁止使用 `arguments.callee`
  - 函数声明必须在顶层
  - 新增了 ES6 保留字（已无意义）

参考文档：[https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode)

## 如何实现图片滚动懒加载

实现滚动懒加载，要利用 `<img>` 的一个特性：如果 `<img>` 没有 `src` 属性或者 `src` 属性为空，浏览器是不会加载这个图片的。所以我们实现懒加载的方法也很简单：`<img>` 的默认 `src` 为空，将真正的 `src` 保存在图片的 `dataset` 或自定义属性上。当 `<img>` 元素进入视图之后，我们再将图片的 `src` 设置为真实的 `src` ，促使图片加载。判断图片位置的方式，可以使用上边题目提到的 `Element.getBoundingClientRect()` 。  
需要注意的是，出于性能考虑，当图片的加载被触发之后，应当移除图片的滚动事件监听器。

## resize 和 scroll 的性能优化

使用 throttle 技术，在一定的时间内只执行一次回调函数。如果连续触发，只有最后一次回调会执行。

```javascript
let resizeTimer = null
window.addEventListener('resize', function() {
  if (resizeTimer !== null) {
    clearTimeout(resizeTimer)
  }
  resizeTimer = setTimeout(function() {
    console.log('resized!')
  }, 1000)
})
```

## 页面加载的性能优化问题

1. 首先要保证前端请求的资源大小在合理范围内，同时为了加快资源加载速度，可以使用 CDN 对依赖文件进行加载。
2. 浏览器的并发量是有限的。前端要考虑的一个很重要的性能优化点，就是对待加载的资源进行排序，以保证最重要的资源能够最先进入加载序列。举个例子，头像等图片资源可以放在关键 JS 文件加载完后，再进行加载。

   ![](../assets/concurrence-limit.png)

3. \[待扩充\]

## throttle 和 debounce 的关系与区别

参考文章：[https://jinlong.github.io/2016/04/24/Debouncing-and-Throttling-Explained-Through-Examples/](https://jinlong.github.io/2016/04/24/Debouncing-and-Throttling-Explained-Through-Examples/)

### debounce 防抖动

debounce 用于解决某一个事件函数被频繁触发造成的性能问题（比如 scroll 事件函数会在 scroll 操作上不停触发），debounce 限制在某一个动作周期中该时间函数只在动作发生的开始 / 结尾执行一次。

leading: 在动作结束后执行  
immediate: 在动作开始时执行

### throttle 节流阀

throttle 同样用于解决类似问题。它限制时间函数在特定时间周期内只执行一次，且一定会执行一次。

## 如何监测 DOM 元素的尺寸变化？

DOM 元素尺寸变化本身不会触发事件。只有 `window`才有`onresize`回调。不过，有两种方案可以解决这个问题:

1. MutationObserver
2. 一个监测 DOM 尺寸变化的第三方库：[https://github.com/wnr/element-resize-detector](https://github.com/wnr/element-resize-detector)

## 两个冷门的 CSS 问题

### \[1, 2\] + \[3, 4\] 的结果是什么

答案：1,23,4

解释：Array 不能直接相加，所以要转换为最相近的可相加类型（String）再相加。Array to String 默认以逗号直接连接。

### typeof \(a && b\) 的结果是什么

答案：如果 a 为假返回 a 的类型，如果 a 为真返回 b 的类型

解释：考察的其实是 a && b 表达式的返回值，与 typeof 关系不大。

## Array 能被直接继承吗？如果不能，为什么？该怎么实现继承？

在 ES5 中是不可以的。因为 ES5 子类继承的是父类的实例，也就是说子类的实例会共享父类的实例，这样会导致 Array 的 `length` 属性行为异常。

在 ES6 中是可以的。

（待扩充）

## Array 的 splice 可不可以添加元素？

可以，看文档！

![](../assets/splice.png)

`Array.prototype.splice` 从第三个参数往后接收的所有参数都是要添加进当前数组的元素。

## 在一个 `<a>` 标签中有一个 `<span>` 标签，点击 `<span>` 触发了点击事件，`target` 是谁？

`target` 是 `<span>` 。
