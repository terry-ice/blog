## Chapter 1: Getting started with HTML5 Canvas(开始使用HTML5 Canvas)
* 检测画布上的鼠标位置
* 画布大小和分辨率
* 旋转
* 将画布保存为图像文件
* 如何将Html5 Canvas元素添加到网页
* Html5画布功能和用途的索引
* 屏幕画布
* Hello World

## 代码分析
### 检测画布上的鼠标位置
此示例将显示如何获取鼠标相对于画布的位置，使得（0,0）将成为HTML5 Canvas的左上角。 e.clientX和e.clientY将获取鼠标相对于文档顶部的位置，将其更改为基于画布的顶部，我们从客户端X和Y中减去画布的左侧和右侧位置。
[Math.round()]()的使用是为了确保x，y位置是整数，因为画布的边界矩形可能没有整数位置。
* 获取canvas画布上下文，设置画布文字
* 给画布添加鼠标经过事件
* 执行getBoundingClientRect(),画布起始位置为（0.0）
* 获取鼠标X和Y的位置通过Math.round()转为正数
* 通过clearRect()清除画布上的矩形区域,之后输出到X和Y的值到坐标为(10,20)的区域显示
### 相关扩展
* [EventTarget.addEventListener()](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener) 方法将指定的监听器注册到 EventTarget 上，当该对象触发指定的事件时，指定的回调函数就会被执行
* [mouseover](https://developer.mozilla.org/zh-CN/docs/Web/Events/mouseover%E4%BA%8B%E4%BB%B6)当指针设备移动到存在监听器的元素或其子元素的时候，mouseover事件就会被触发。
* [MouseEvent.clientX](https://developer.mozilla.org/zh-CN/docs/Web/API/MouseEvent/clientX) MouseEvent.clientX 是只读属性， 它提供事件发生时的应用客户端区域的水平坐标 (与页面坐标不同)。
* [MouseEvent.clientY](https://developer.mozilla.org/zh-CN/docs/Web/API/MouseEvent/clientY)是只读属性， 它提供事件发生时的应用客户端区域的垂直坐标 (与页面坐标不同)。  
* [Math.round()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Math/round) 函数返回一个数字四舍五入后最接近的整数。
* [Range.getBoundingClientRect(https://developer.mozilla.org/zh-CN/docs/Web/API/Range/getBoundingClientRect)]() 返回一个 ClientRect 对象，该对象限定了选定的文档对象的内容，该方法返回了一个矩形，这个矩形包围了该文档对象中所有元素的边界矩形集合
* [clearRect()](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/clearRect)是 Canvas 2D API 设置指定矩形区域内（以 点 (x, y) 为起点，范围是(width, height) ）所有像素变成透明，并擦除之前绘制的所有内容的方法。
* [fillText()](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/fillText)是 Canvas 2D API 在 (x, y)位置填充文本的方法。如果选项的第四个参数提供了最大宽度，文本会进行缩放以适应最大宽度。






