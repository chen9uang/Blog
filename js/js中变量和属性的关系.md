## 变量 VS 属性

在 javascript 中什么是属性？什么是变量？他们有什么不同？     

### 变量对象（The VariableObject）

在理解变量之前，我们需要了解什么是变量对象。在 javascript 中代码可以在全局上下文、函数上下文、和 eval 上下文中执行。只存在一个全局上下文，每次调用函数都会有一个函数执行上下文。每个执行上下文都有一个与之关联变量对象。变量（函数）都会作为当前上下文的变量对象的属性而创建。   

全局上下文的变量对象就是全局对象，在浏览器环境下就是window:

``` js
  var a = 'hello world'
  // window is the global VariableObject
  window.a // hello world
```
在函数的上下文中变量对象更加的棘手。每个函数上下文都有一个变量对象叫作激活对象（ActivationObject）。但是你不能像全局对象那样直接引用他。你只要知道有这个东西，因此在函数里面创建一个变量你是不能直接像属性一样引用它的。

``` js
  function foo() {
    var bar = "hello world";
    window.bar; // undefined (VariableObject is not window)
  }
```

### 什么是属性？

>ECMA 5: An association between a name and a value that is a part of an object. [4.3.26]   

属性是对象的一部分，描述了一个名字和值之间的关联。   

``` js
  // 属性的栗子
  foo.bar = "baz";
  window.alert;
  a.b = function(c,d,e) {return (c * d) + e};
  Math.PI;
  myArray[5];
```

### 什么是变量?

ECMA5没有明确的定义。   

我们试想：变量是在执行上下文中的描述名字和值之间的关联。

现在我们能明确的知道属性和变量的区别。属性是属于对象的，而变量是属于上下文（而上下文刚好有一个对象表示--即：变量对象）    

``` js
  // 变量的栗子
  var bar = 2; // 全局上下文
  function foo = function() {
    var a; // 函数上下文
    f = 4; // 无意创建的全局上下文
  }
```

### 所以变量和属性可以互相转换对吗？

不全对，尽管它可能发生   

``` js
  // 定义一个属性，通过变量获取
  window.a = 'hello world'
  a // 'hello world'

  // 定义一个变量，通过属性获取
  var a = 'hello world'
  window.a // hello world
```
这边能够通过互相转换的方式获取到值仅仅是因为全局上下文的变量对象刚好是全局对象（window），即（window === VariableObject）。在函数中就不是了，所以函数中这样是不行的。   

### 所以我们应该注意什么？

这里有几个行为差异会影响对象组成（？）和程序流程   

#### 变量提升

在当前作用域执行的时候，变量和函数会作为变量对象的属性最开始的时候被创建。而赋值只有在程序执行到这个地方的时候才会进行。

``` js
  alert(a); //undefined (no error)
  alert(b); //ReferenceError: b is not defined
  var a = 24;
  window.b = 36;
```

值得注意的：   

1. 声明变量 a 被提升，但不是赋值   

2. 我们可以通过 alert(window.b)来避免引用错误。如果我们不是通过属性的方式引用变量，那么就会通过当前的变量对象的属性来引用，当变量对象没有找到这个标识（b）就会报引用错误。   

#### 属性初始化

每一个属性都会有它默认的描述符。属性描述符定义了几个属性(其中 value 是最显而易见的)，在 ECMA5中他们分别是:[[Writable]]，[[Enumeraable]]，[[Configurable]]    

为了简化，现在重点关注的是和这次话题有关的[[Configurable]] 属性。   

**当你创建一个变量的时候，[[Configurable]]会设置成 false。当你显式创建一个属性的时候，[[Configurable]]默认为 true**

``` js
  // 变量
  var oneTimeInit = function() {
    //do stuff
  }
  delete oneTimeInit; //false (删除不成功)
  typeof oneTimeInit; "function";
  
  // 显示定义属性
  window.oneTimeInit = function() {
    //do stuff
  }
  delete oneTimeInit; //true (删除成功)
  typeof oneTimeInit; "undefined";
```

### 其他类型的变量

函数的arguments对象，每个形参都会被添加到激活对象（VariableObject）里面。函数声明也是上下文对象的属性，所以某种意义上也可以称之为变量。   

### 有几种声明属性的方式

  至少5种

``` js
  // dot notation
  window.foo = 'hello';
  
  // subscript notation
  window['foo'] = 'hello';
  
  // forgetting to use the var keyword
  var bar = function() {
    foo = "hello";
  }
  
  // Using ECMA 5 methods (showing limited use of property attributes for clarity)
  //runs in chrome, safari and IE8 (IE8 works for DOM objects only)
  Object.defineProperty(window,"foo", {value: "hello"});
  
  // runs in chrome and safari
  Object.defineProperties(window, {"foo": {value: "hello"},"bar"
```

### 如何理解eval上下文？

eval代码中采用的是包含 eval 环境的上下文。   

在下面的栗子中，blah、foo、bar 都是当前函数激活对象（ActivationObject）的属性，虽然 bar 是在 eval 里面。   

``` js
  var baz = function(blah) {
  var foo = 34 * blah
  eval('var bar =' + MY_SPECIAL_CODE)
}
```
值得注意的是：eval 里面创建的变量[[Configurable]]属性默认为 true ,也就是说可以用 delete 操作符删除它，这在其他地方是不行的。

``` js
  eval('var bar = ' + n)
  delete bar // true
  typeof bar // undefined
```

但是 eval 似乎因为安全原因不让用了，所以了解下就好。

[原文](https://javascriptweblog.wordpress.com/2010/08/09/variables-vs-properties-in-javascript/)