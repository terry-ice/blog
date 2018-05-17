##  对象
### 语法
对象可以通过两种形式定义:声明(文字)形式和构造形式。
对象的文字语法大概是这样:
```js
var myObj = {
  key: value
};
```
构造形式大概是这样:
```js
var myObj = new Object();
myObj.key = value;
```
唯一的区别是，在文字声明中你可以添加多个 键 / 值对，但是在构造形式中你必须逐个添加属性。文字语法常用
### 类型
在javascript中一共有六种主要类型
* string 
* number 
* boolean 
* null
* undefined 
* object
简单基本类型(string、boolean、number、null 和 undefined)本身并不是对象。
null 有时会被当作一种对象类型，但是这其实只是语言本身的一个 bug，即对 null 执行 typeof null 时会返回字符串 "object"。1 实际上，null 本身是基本类型。
### 内置对象
JavaScript 中还有一些对象子类型，通常被称为内置对象。
* String
* Number
* Boolean 
* Object
* Function 
* Array
* Date
* RegExp 
* Error
但是在 JavaScript 中，它们实际上只是一些内置函数。
```js
var strPrimitive = "I am a string";
typeof strPrimitive; // "string" 
strPrimitive instanceof String; // false
var strObject = new String( "I am a string" );
typeof strObject; // "object"
strObject instanceof String; // true
// 检查 sub-type 对象
Object.prototype.toString.call( strObject ); // [object String]
```
子类型在内部借用了 Object 中的 toString() 方法。原始值 "I am a string" 并不是一个对象，它只是一个字面量，并且是一个不可变的值。 如果要在这个字面量上执行一些操作，比如获取长度、访问其中某个字符等，那需要将其 转换为 String 对象。如果使用类似 42.359.toFixed(2) 的方法，引擎会把42 转换成 new Number(42)。对于布尔字面量来说也是如此。  
null 和 undefined 没有对应的构造形式，它们只有文字形式。相反，Date 只有构造，没有
文字形式。对于 Object、Array、Function 和 RegExp(正则表达式)来说，无论使用文字形式还是构 造形式，它们都是对象，不是字面量。
### 内容
对象的内容是由一些存储在特定命名位置的(任意类型的)值组成的，我们称之为属性。
存储在对象容器内部的是这些属性的名称，它们值就像指针(从技术角度 来说就是引用)一样，指向这些值真正的存储位置。
```js
var myObject = {
   a: 2
};
myObject.a; // 2 //属性访问
myObject["a"]; // 2 //键访问
```
.a 语法通 常被称为“属性访问”，["a"] 语法通常被称为“键访问”。
在对象中，属性名永远都是字符串。如果你使用 string(字面量)以外的其他值作为属性名，那它首先会被转换为一个字符串。即使是数字也不例外。
虽然在数组下标中使用的的 确是数字，但是在对象属性名中数字会被转换成字符串，所以当心不要搞混对象和数组中数字的用法。
```js
var myObject = { };
myObject[true] = "foo";
myObject[3] = "bar"; 
myObject[myObject] = "baz";
myObject["true"]; // "foo"
myObject["3"]; // "bar"
myObject["[object Object]"]; // "baz"
```
### 属性与方法
如果访问的对象属性是一个函数，有些开发者喜欢使用不一样的叫法以作区分。由于函数 很容易被认为是属于某个对象，在其他语言中，属于对象(也被称为“类”)的函数通常 被称为“方法”，因此把“属性访问”说成是“方法访问”也就不奇怪了。
即使你在对象的文字形式中声明一个函数表达式，这个函数也不会“属于”这个对象—— 它们只是对于相同函数对象的多个引用。
```js
var myObject = {
  foo: function() {
    onsole.log( "foo" );
  }
};
var someFoo = myObject.foo;
someFoo; // function foo(){..} 
myObject.foo; // function foo(){..}
```
### 复制对象
#### 深复制
对于 JSON 安全(也就是说可以被序列化为一个 JSON 字符串并且可以根据这个字符串解 析出一个结构和值完全一样的对象)的对象来说，有一种巧妙的复制方法:
```js
var newObj = JSON.parse( JSON.stringify( someObj ) );
```
当然，这种方法需要保证对象是 JSON 安全的，所以只适用于部分情况。
#### 浅复制
相比深复制，浅复制非常易懂并且问题要少得多，所以 ES6 定义了 Object.assign(..) 方 法来实现浅复制。Object.assign(..) 方法的第一个参数是目标对象，之后还可以跟一个 或多个源对象。它会遍历一个或多个源对象的所有可枚举(enumerable，参见下面的代码) 的自有键(owned key，很快会介绍)并把它们复制(使用 = 操作符赋值)到目标对象，最 后返回目标对象，就像这样:
```js
var newObj = Object.assign( {}, myObject );
newObj.a; // 2
newObj.b === anotherObject; // true
newObj.c === anotherArray; // true
newObj.d === anotherFunction; // true
```
### 属性描述符
从 ES5 开始，所有的属性都具备了属性描述符
* writable(可写)
* enumerable(可枚举)
* configurable(可配置)
```js
var myObject = { a:2
};
Object.getOwnPropertyDescriptor( myObject, "a" );
// {
// value: 2,
// writable: true,
// enumerable: true,
// configurable: true
// }
```
在创建普通属性时属性描述符会使用默认值，我们也可以使用 Object.defineProperty(..) 来添加一个新属性或者修改一个已有属性(如果它是 configurable)并对特性进行设置。
```js
var myObject = {};
Object.defineProperty( myObject, "a", {
  value: 2,
  writable: true,
  configurable: true,
  enumerable: true
});
myObject.a; // 2
```
*  Writable
writable 决定是否可以修改属性的值。
* Configurable
只要属性是可配置的，就可以使用 defineProperty(..) 方法来修改属性描述符:
* Enumerable
这个描述符控制的是属性是否会出现在对象的属性枚举中，比如说 for..in 循环。  
如果把 enumerable 设置成 false，这个属性就不会出现在枚举中，虽然仍 然可以正常访问它。相对地，设置成 true 就会让它出现在枚举中。
###  不变性
有时候你会希望属性或者对象是不可改变(无论有意还是无意)的，在 ES5 中可以通过很
多种方法来实现。
很重要的一点是，所有的方法创建的都是浅不变形，也就是说，它们只会影响目标对象和 它的直接属性。如果目标对象引用了其他对象(数组、对象、函数，等)，其他对象的内 容不受影响，仍然是可变的:
```js
     myImmutableObject.foo; // [1,2,3]
     myImmutableObject.foo.push( 4 );
     myImmutableObject.foo; // [1,2,3,4]
```

假设代码中的 myImmutableObject 已经被创建而且是不可变的，但是为了保护它的内容 myImmutableObject.foo，你还需要使用下面的方法让 foo 也不可变。
在 JavaScript 程序中很少需要深不可变性。有些特殊情况可能需要这样做， 但是根据通用的设计模式，如果你发现需要密封或者冻结所有的对象，那 你或许应当退一步，重新思考一下程序的设计，让它能更好地应对对象值 的改变。
1. 对象常量
结合 writable:false 和 configurable:false 就可以创建一个真正的常量属性(不可修改、 重定义或者删除):
```js
var myObject = {};
Object.defineProperty( myObject, "FAVORITE_NUMBER", {
  value: 42,
  writable: false,
  configurable: false }
);
```
2. 禁止扩展
如果你想禁止一个对象添加新属性并且保留已有属性，可以使用 Object.prevent Extensions(..):
```js
var myObject = { a:2};
     Object.preventExtensions( myObject );
     myObject.b = 3;
     myObject.b; // undefined
```
在非严格模式下，创建属性 b 会静默失败。在严格模式下，将会抛出 TypeError 错误。
3. 密封
Object.seal(..) 会创建一个“密封”的对象，这个方法实际上会在一个现有对象上调用 Object.preventExtensions(..) 并把所有现有属性标记为 configurable:false。
所以，密封之后不仅不能添加新属性，也不能重新配置或者删除任何现有属性(虽然可以 修改属性的值)。
4. 冻结
Object.freeze(..) 会创建一个冻结对象，这个方法实际上会在一个现有对象上调用 Object.seal(..) 并把所有“数据访问”属性标记为 writable:false，这样就无法修改它们 的值。
这个方法是你可以应用在对象上的级别最高的不可变性，它会禁止对于对象本身及其任意 直接属性的修改(不过就像我们之前说过的，这个对象引用的其他对象是不受影响的)。
你可以“深度冻结”一个对象，具体方法为，首先在这个对象上调用 Object.freeze(..)， 然后遍历它引用的所有对象并在这些对象上调用 Object.freeze(..)。但是一定要小心，因 为这样做有可能会在无意中冻结其他(共享)对象。
### Getter和Setter
对象默认的 [[Put]] 和 [[Get]] 操作分别可以控制属性值的设置和获取。  
当你给一个属性定义 getter、setter 或者两者都有时，这个属性会被定义为“访问描述 符”(和“数据描述符”相对)。对于访问描述符来说，JavaScript 会忽略它们的 value 和 writable 特性，取而代之的是关心 set 和 get(还有 configurable 和 enumerable)特性。
```js
var myObject = {
  // 给 a 定义一个 getter
  get a () {
    return 2
  }
}
Object.defineProperty(myObject, {
  // 目标对象 "b", // 属性名
  // 描述符 给 b 设置一个 getter
  get: function () {
    return this.a * 2
  },
  // 确保 b 会出现在对象的属性列表中
  enumerable: true
})
myObject.a // 2
myObject.b // 4
```
由于我们只定义了 a 的 getter，所以对 a 的值进行设置时 set 操作会忽略赋值操作，不会抛 出错误。而且即便有合法的 setter，由于我们自定义的 getter 只会返回 2，所以 set 操作是 没有意义的。
为了让属性更合理，还应当定义 setter，和你期望的一样，setter 会覆盖单个属性默认的 [[Put]](也被称为赋值)操作。通常来说 getter 和 setter 是成对出现的(只定义一个的话 通常会产生意料之外的行为):
```js
var myObject = {
  // 给 a 定义一个 getter
  get a () {
    return this._a_
  },
  // 给 a 定义一个 setter
  set a (val) {
    this._a_ = val * 2
  }
}
myObject.a = 2
myObject.a // 4
```
### 存在性

前面我们介绍过，如 myObject.a 的属性访问返回值可能是 undefined，但是这个值有可能 是属性中存储的 undefined，也可能是因为属性不存在所以返回 undefined。那么如何区分 这两种情况呢?
```js
var myObject = {
  a: 2
}
console.log('a' in myObject) // true
console.log('b' in myObject) // false
myObject.hasOwnProperty('a') // true
myObject.hasOwnProperty('b') // false
```
in 操作符会检查属性是否在对象及其 [[Prototype]] 原型链中。相比之下， hasOwnProperty(..) 只会检查属性是否在 myObject 对象中，不会检查 [[Prototype]] 链。所有的普通对象都可以通过对于 Object.prototype 的委托来访问 hasOwnProperty(..)， 但是有的对象可能没有连接到Object.prototype( 通 过 Object. create(null) 来创建—)。在这种情况下，形myObejct.hasOwnProperty(..) 就会失败。  
这时可以使用一种更加强硬的方法来进行判断:Object.prototype.hasOwnProperty. call(myObject,"a")，它借用基础的 hasOwnProperty(..) 方法并把它显式绑定到 myObject 上。


## 致谢
* 整理内容来自<<你不知道的javascript上卷>>
* 对你有帮助，请帮忙star
