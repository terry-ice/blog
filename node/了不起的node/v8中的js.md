## 全新的 javascript 引擎 v8

随着 chrome 浏览器的发布，带来了全新的 javaScrpt 引擎 V8，它急速的执行环境，加之时刻保持最新并支持最新的 ECMAScript 特性的优势，快速占领了市场。

### Object

在 V8 中获取对象所有的自有键值，

```js
var a = { a: "b", c: "d" };
Object.keys(a); // ['a','c']
```

### isArray

在判断数组用 typeof 的时候回返回 object。无法区分是否是真的数组
Array.isArray 对数组返回 true，对其他值则会返回 false;

```js
Array.isArray(new Array()); //true
Array.isArray([]); //true
Array.isArray(null); //false
Array.isArray(arguments); //false
```

### 数组方法

#### 要遍历数组可以用 forEach

```js
[1, 2, 3].forEach(v => {
  console.log(v);
});
```

#### 数组过滤

```js
[1,2,3，4，5，6].filter(v => v > 3 ); //返回 [4,5,6]
```

#### 操作数组

```
[1,2,3,4].map(v => v * 2 );
```

V8 还提供了一些不太常用的方法 reduce、reduceRight、lastIndexOf

### 字符串方法

去除字符串首尾的空格

```js
" hello terry ".trim(); // hello terry
```

### JSON

JSON.stringify 和 JSON.parse 方法可以对 JSON 进行数据解码和编码。
JSON 是一种编码标准，用户大部分的 Web 服务和 API 服务

### function

#### bind

bind 可以用来改变对 this 的引用

```js
function a() {
  this.hello == "word"; // true
}
var b = a.bind({ hello: "world" });
b();
```

#### name

V8 中无法为函数引用指派名字,然而如果对函数进行了命名，V8 就能够在显示堆栈追踪信息时把名字显示出来

```js
> var woot = function buggy (){ throw new Error(); };
> woot();
Error at buggy ([object context]:1:34)
```

#### **PROTO**(继承)

**proto**使得定义继承链更加容易
function a (){}
function b (){}
b.prototype.**proto** = Animal.prototype;

### 存取器

你可以通过调用方法来定义属性，访问属性使用**defineGetter**、设置属性使用**defineSetter**
