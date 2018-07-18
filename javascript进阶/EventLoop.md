* JavaScript 的并发模型基于"事件循环"。这个模型与像 C 或者 Java 这种其它语言中的模型截然不同。

## 运行时概念
下面的内容解释了一个理论上的模型。现代 JavaScript 引擎着重实现和优化了描述的几个语义。
### 可视化描述
![cosnole.log](./static/loop.svg)
### 栈
函数调用形成了一个栈帧。
```js
function foo(b) {
  var a = 10;
  return a + b + 11;
}

function bar(x) {
  var y = 3;
  return foo(x * y);
}

console.log(bar(7));
```
当调用bar时，创建了第一个帧 ，帧中包含了bar的参数和局部变量。当bar调用foo时，第二个帧就被创建，并被压到第一个帧之上，帧中包含了foo的参数和局部变量。当foo返回时，最上层的帧就被弹出栈（剩下bar函数的调用帧 ）。当bar返回的时候，栈就空了。

### 堆
对象被分配在一个堆中，即用以表示一个大部分非结构化的内存区域。
### 队列
一个 JavaScript 运行时包含了一个待处理的消息队列。每一个消息都有一个为了处理这个消息相关联的函数。

在事件循环时，runtime （运行时）总是从最先进入队列的一个消息开始处理队列中的消息。正因如此，这个消息就会被移出队列，并将其作为输入参数调用与之关联的函数。为了使用这个函数，调用一个函数总是会为其创造一个新的栈帧（ stack frame），一如既往。

函数的处理会一直进行直到执行栈再次为空；然后事件循环（event loop）将会处理队列中的下一个消息（如果还有的话）。

## 事件循环
之所以称为事件循环，是因为它经常被用于类似如下的方式来实现：
```js
while (queue.waitForMessage()) {
  queue.processNextMessage();
}
```
如果当前没有任何消息queue.waitForMessage 会等待同步消息到达。
### 执行至完成
每一个消息完整的执行后，其它消息才会被执行。当你分析你的程序时，这点提供了一些优秀的特性，包括每当一个函数运行时，它就不能被抢占，并且在其他代码运行之前完全运行（且可以修改此函数操作的数据）。这与C语言不同，例如，如果函数在线程中运行，则可以在任何位置终止然后在另一个线程中运行其他代码。

这个模型的一个缺点在于当一个消息需要太长时间才能完成，Web应用无法处理用户的交互，例如点击或滚动。浏览器用“程序需要过长时间运行”的对话框来缓解这个问题。一个很好的做法是使消息处理缩短，如果可能，将一个消息裁剪成几个消息。

### 添加消息
在浏览器里，当一个事件出现且有一个事件监听器被绑定时，消息会被随时添加。如果没有事件监听器，事件会丢失。所以点击一个附带点击事件处理函数的元素会添加一个消息。其它事件亦然。

调用 setTimeout 函数会在一个时间段过去后在队列中添加一个消息。这个时间段作为函数的第二个参数被传入。如果队列中没有其它消息，消息会被马上处理。但是，如果有其它消息，setTimeout消息必须等待其它消息处理完。因此第二个参数仅仅表示最少的时间 而非确切的时间。

### 零延迟
零延迟并不是意味着回调会立即执行。在零延迟调用 setTimeout 时，其并不是过了给定的时间间隔后就马上执行回调函数。其等待的时间基于队列里正在等待的消息数量。在下面的例子中，"this is just a message" 将会在回调 (callback) 获得处理之前输出到控制台，这是因为延迟是要求运行时 (runtime) 处理请求所需的最小时间，但不是有所保证的时间。
```js
(function() {

  console.log('this is the start');

  setTimeout(function cb() {
    console.log('this is a msg from call back');
  });

  console.log('this is just a message');

  setTimeout(function cb1() {
    console.log('this is a msg from call back1');
  }, 0);

  console.log('this is the end');

})();

// "this is the start"
// "this is just a message"
// "this is the end"
// note that function return, which is undefined, happens here 
// "this is a msg from call back"
// "this is a msg from call back1"
```
### 多个运行时互相通信
JavaScript 的一个非常有趣的特性是事件循环模型，与许多其他语言不同，它永不阻塞。 处理 I/O 通常通过事件和回调来执行，所以当一个应用正等待IndexedDB查询返回或者一个 XHR 请求返回时，它仍然可以处理其它事情，如用户输入。

例外是存在的，如 alert或者同步 XHR，但应该尽量避免使用它们。注意，例外的例外也是存在的（但通常是实现错误而非其它原因）。

[EventLoop](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/EventLoop)