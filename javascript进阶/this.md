## this 是啥玩意儿？

this 关键字是 JavaScript 中最复杂的机制之一。它是一个很特别的关键字，被自动定义在 所有函数的作用域中。但是即使是非常有经验的 JavaScript 开发者也很难说清它到底指向什么。

* this 是在运行时绑定的，它的上下文取决于函数调 用时的各种条件。this 的绑定和函数声明的位置没有任何关系，只取决于函数的调用方式。
* 当一个函数被调用时，会创建一个活动记录(有时候也称为执行上下文)。这个记录会包含函数在哪里被调用(调用栈)、函数的调用方法、传入的参数等信息。this 就是记录的其中一个属性，会在函数执行的过程中用到。
* this 既不指向函数自身也不指向函数的词法作用域。
* this 实际上是在函数被调用时发生的绑定，它指向什么完全取决于函数在哪里被调用。

## this 的调用位置

* 在JavaScript中,调用栈就是一系列的函数,表明当前函数是由哪些上层函数调用
* 调用位置就是寻找“函数被调用的位置”

```js
function aaa () {
  console.trace()
  bbb()// <-- bbb 的调用位置
} 
function bbb () {
  // 当前调用栈是 aaa -> bbb 因此，当前调用位置在 aaa 中
  console.trace()
  ccc() // <-- ccc 的调用位置
}
function ccc () {
  // 当前调用栈是 aaa -> bbb -> ccc 因此，当前调用位置在 bbb 中
  console.trace()
}
aaa() // <-- aaa 的调用位置
```
注意我们是如何(从调用栈中)分析出真正的调用位置的，因为它决定了 this 的绑定。
在调试工具中,我们可以直接使用console.trace()来打印出调用栈。
### 笔记
你可以把调用栈想象成一个函数调用链，就像我们在前面代码段的注释中所 写的一样。但是这种方法非常麻烦并且容易出错。另一个查看调用栈的方法 是使用浏览器的调试工具。绝大多数现代桌面浏览器都内置了开发者工具， 其中包含 JavaScript 调试器。就本例来说，你可以在工具中给 foo() 函数的 第一行代码设置一个断点，或者直接在第一行代码之前插入一条 debugger; 语句。运行代码时，调试器会在那个位置暂停，同时会展示当前位置的函数 调用列表，这就是你的调用栈。因此，如果你想要分析 this 的绑定，使用开 发者工具得到调用栈，然后找到栈中第二个元素，这就是真正的调用位置。
## this 绑定规则
### 默认绑定
```js
function foo() {
  console.log( this.a );
}
var a = 2;
foo(); // 2
```
函数调用时应用了 this 的默认绑定，因此 this 指向全局对象,所以this.a 被解析成了全局变量 a。
如果使用严格模式(strict mode)，那么全局对象将无法使用默认绑定，因此 this 会绑定 到 undefined
### 隐式绑定
需要考虑的规则是调用位置是否有上下文对象，隐式绑定时，我们必须在一个对象内部包含一个指向函
数的属性，并通过这个属性间接引用函数，从而把 this 间接(隐式)绑定到这个对象上。
```js
function foo () {
  console.log(this.a)
}
var obj = {
  a: 2,
  foo: foo
}
obj.foo() // 2
```
当 foo() 被调用时，它的落脚点确实指向 obj 对象。当函数引用有上下文对象时，隐式绑定规则会把函数调用中的 this 绑定到这个上下文对象。因为调 用 foo() 时 this 被绑定到 obj，因此 this.a 和 obj.a 是一样的。
#### 隐式丢失
一个最常见的 this 绑定问题就是被隐式绑定的函数会丢失绑定对象，也就是说它会应用默认绑定，从而把 this 绑定到全局对象或者 undefined 上，取决于是否是严格模式。
```js
function foo () {
  console.log(this.a)
}
var obj = { a: 2,
  foo: foo 
}
var bar = obj.foo // 函数别名!
var a = 'oops, global' // a 是全局对象的属性
bar(); // "oops, global"
```
虽然 bar 是 obj.foo 的一个引用，但是实际上，它引用的是 foo 函数本身，因此此时的 bar() 其实是一个不带任何修饰的函数调用，因此应用了默认绑定。
### 显式绑定
JavaScript 中的“所有”函数都有一些有用的特性(这和它们的 [[ 原型 ]] 有关)，可以用来解决这个问题。具体点说，可以使用函数的 call(..) 和 apply(..) 方法。严格来说，JavaScript 的宿主环境有时会提供一些非常特殊的函数，它们 并没有这两个方法。但是这样的函数非常罕见，JavaScript 提供的绝大多数函数以及你自 己创建的所有函数都可以使用 call(..) 和 apply(..) 方法。
这两个方法是如何工作的呢?它们的第一个参数是一个对象，它们会把这个对象绑定到 this，接着在调用函数时指定这个 this。因为你可以直接指定 this 的绑定对象，因此我 们称之为显式绑定。
```js
function foo () {
  console.log(this.a)
}
var obj = {
  a: 2
}
foo.call(obj) // 2
```
通过 foo.call(..)，我们可以在调用 foo 时强制把它的 this 绑定到 obj 上。
从 this 绑定的角度来说，call(..) 和 apply(..) 是一样的，它们的区别体现 在其他的参数上，但是现在我们不用考虑这些。
#### 硬绑定(强制绑定)
可惜，显式绑定仍然无法解决我们之前提出的丢失绑定问题。解决办法就是硬绑定
```js
function foo () {
  console.log(this.a)
}
var obj = {
  a: 2
}
var bar = function () {
  foo.call(obj)
}
bar() // 2
setTimeout(bar, 100) // 2
// 硬绑定的 bar 不可能再修改它的 this
bar.call(window) // 2
```
我们来看看这个变种到底是怎样工作的。我们创建了函数 bar()，并在它的内部手动调用 了 foo.call(obj)，因此强制把 foo 的 this 绑定到了 obj。无论之后如何调用函数 bar，它 总会手动在 obj 上调用 foo。这种绑定是一种显式的强制绑定，因此我们称之为硬绑定。
由于硬绑定是一种非常常用的模式，所以在 ES5 中提供了内置的方法 Function.prototype. bind，它的用法如下:
```js
function foo (something) {
  console.log(this.a, something)
  return this.a + something
}
var obj = {
  a: 2
}
var bar = foo.bind(obj)
var b = bar(3) // 2 3
console.log(b) // 5
```
bind(..) 会返回一个硬编码的新函数，它会把参数设置为 this 的上下文并调用原始函数。
#### API调用的“上下文”
第三方库的许多函数，以及 JavaScript 语言和宿主环境中许多新的内置函数，都提供了一 个可选的参数，通常被称为“上下文”(context)，其作用和 bind(..) 一样，确保你的回调 函数使用指定的 this。
举例来说:
```js
function foo(el) {
  console.log( el, this.id )
}
var obj = {
id: "awesome"
}
// 调用 foo(..) 时把 this 绑定到 
obj [1, 2, 3].forEach( foo, obj )
// 1 awesome 2 awesome 3 awesome
```
这些函数实际上就是通过 call(..) 或者 apply(..) 实现了显式绑定，这样你可以少些一些 代码。
注意：call()方法的作用和 apply() 方法类似，只有一个区别，就是 call()方法接受的是若干个参数的列表，而apply()方法接受的是一个包含多个参数的数组。
* [forEach()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)方法对数组的每个元素执行一次提供的函数。
* [call()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/call)方法调用一个函数, 其具有一个指定的this值和分别地提供的参数(参数的列表)。
* [apply()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)方法调用一个函数, 其具有一个指定的this值，以及作为一个数组（或类似数组的对象）提供的参数。
* [bind()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)方法创建一个新的函数, 当被调用时，将其this关键字设置为提供的值，在调用新函数时，在任何提供之前提供一个给定的参数序列。
### new绑定
在传统的面向类的语言中，“构造函数”是类中的一些特殊方法，使用 new 初始化类时会
调用类中的构造函数。通常的形式是这样的:
something = new MyClass(..);
JavaScript 也有一个 new 操作符，使用方法看起来也和那些面向类的语言一样，绝大多数开 发者都认为 JavaScript 中 new 的机制也和那些语言一样。然而，JavaScript 中 new 的机制实 际上和面向类的语言完全不同。
使用 new 来调用函数，或者说发生构造函数调用时，会自动执行下面的操作。
* 创建(或者说构造)一个全新的对象。
* 这个新对象会被执行[[原型]]连接。
* 这个新对象会绑定到函数调用的this。
* 如果函数没有返回其他对象，那么new表达式中的函数调用会自动返回这个新对象。
```js
function foo (a) {
  this.a = a
}
var bar = new foo(2)
console.log(bar.a) // 2
```
使用 new 来调用 foo(..) 时，我们会构造一个新对象并把它绑定到 foo(..) 调用中的 this 上。new 是最后一种可以影响函数调用时 this 绑定行为的方法，我们称之为 new 绑定。
## 优先级
显式绑定>new 绑定>隐式绑定>默认绑定

## 致谢
* 整理内容来自<<你不知道的javascript上卷>>
* 对你有帮助，请帮忙star
