文档对象模型（DOM）是一个独立语言的，用于操作XML和HTML文档的程序接口（API）。在浏览器中，主要用来与HTML文档打交道，同样也是在web程序中获取XML文档，并使用DOM API用来访问文档的数据。
<!--more-->
用脚本进行DOM操作的代价很昂贵，它是web应用中最常见的性能瓶颈。
浏览器中通常会把DOM和javascript独立实现。
* IE中javascript的实现名称为JScript，位于jscript.dll文件中，DOM的实现在另外一个库，名称为mshtml.dll
* Safari中的DOM和渲染是使用Webkit中的WebCore实现，javascript部分是由独立的javaScriptCore来实现。
* GoogleChrome同样使用Webkit中的WebCore实现，但js部分是自己研发的v8引擎。
### 为什么慢
简单来说，2个独立相关的功能只要是通过接口彼此连接，就会产生一定的消耗。把DOM和javascript想象成2个不同的岛屿，他们之间用收费的桥梁连接，每次访问DOM都要经过这座桥，缴纳“过桥费”，访问越多，费用越高，性能消耗越大。因此，推荐尽可能的减少过桥的次数。
### 重绘和重排
* DOM树表示页面结构
* 渲染树表示DOM节点如何显示
* 一旦DOM和渲染树构建完成，浏览器就开始显示(绘制“paint”)页面元素。
当DOM的变化影响了元素的几何属性（宽和高）---比如改变边框的宽度或者给段落增加文字，导致行数增加---浏览器需要重新计算元素的几何属性，同样其他的元素的几何属性和位置也会因此受到影响。浏览器会是渲染树受到影响的部分失效，并重新构造渲染树。这个过程称为“重排”。完成重排后，浏览器会重新绘制受影响的部分到屏幕中，这个过程称为“重绘（repaint）”
并不是所有的DOM变化都会影响几何属性，例如，改变一个元素的背景色并不会影响它的宽和高。在这种情况下，只会发生一次重绘（不需要重排），因为元素的布局没有改变。尽可能的减少重绘和重排来提高性能。
#### 重排何时发生
几何属性发生变化就需要重排
* 添加或者删除可见的DOM元素
* 元素位置改变
* 元素尺寸改变（包括：外边距，内边距，边框厚度、宽度、高度等属性改变）。
* 内容改变，例如：文本改变或者图片被一个不同尺寸的图片替换
* 页面渲染器初始化
* 浏览器窗口尺寸改变
#### 渲染树变化的排队与刷新
 由于每次重排都产生计算消耗，大多数浏览器通过队列化修改并批量执行来优化重排的过程。
* offsetTop，offsetLeft，offsetWidth，offsetHeight
* scrollTop，scrollLeft，scrollWidth，scrollHeight
* clientTop，clientLeft，clientWidth，clientHeight
以上属性的方法需要返回最新的布局信息，因此浏览器不得不执行渲染队列的”待处理变化“并处罚金重排以返回正确的值。
#### 优化重排和重绘
改变样式，实例中有3个样式属性被改变，每一个都会影响元素的几何属性引发三次重排。
```js
//优化前
var el  = document.getElementById('div')
el.style.borderLeft ="1px"
el.style.borderRight="1px"
el.style.padding = "3px"

//优化后
var el  = document.getElementById('div')
el.style.cssText ="border-left:1px;border-right:1px;padding:3px;"
```
#### 批量修改DOM
当你需要对DOm元素进行一系列操作，可以通过下面的步骤来减少重绘和重排
* 使元素脱离文档流
* 对齐应用多重改变
* 把元素带回文档中。
有三种基本方法可以使DOM脱离文档；
* 隐藏元素---应用修改----重新显示。
* 使用文采偏大在DOM之外构建一个子树，之后插入到文档中
* 将原始元素拷贝到一个脱离文档额节点中，修改副本，完成后再替换原始元素。

### 结语
访问和操作DOM是现代WEB应用的重要部分。但是每次穿越连接都会有性能消耗，提高性能可以注意以下点：
* 最小化DOM访问次数，尽可能在javascript端处理
* 如果需要多次访问DOM节点，请使用局部变量储存它的引用
* 小心处理HTML组合，因为它实时联系着底层文档。把集合的长度缓存在一个变量中，迭代的使用它。
* 如果可能的话，使用速度更快的api，例如querySelectAll()和firstElementChild
* 留意重绘和重排，批量修改样式时候，离线操作DOM树，使用缓存，并减少访问布局信息的次数。
* 动画中使用绝对定位，使用拖放代理。
* 使用时间委托来减少事件处理的数量。
