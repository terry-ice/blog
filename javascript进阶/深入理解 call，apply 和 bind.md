在JavaScript 中，call、apply 和 bind 是 Function 对象自带的三个方法，这三个方法的主要作用是改变函数中的 this 指向，从而可以达到`接花移木`的效果。本文将对这三个方法进行详细的讲解，并列出几个经典应用场景。

1、call(thisArgs [,args...])
该方法可以传递一个thisArgs参数和一个参数列表，thisArgs 指定了函数在运行期的调用者，也就是函数中的 this 对象，而参数列表会被传入调用函数中。thisArgs 的取值有以下四种情况：

不传，或者传null,undefined， 函数中的 this 指向 window 对象
传递另一个函数的函数名，函数中的 this 指向这个函数的引用
传递字符串、数值或布尔类型等基础类型，函数中的 this 指向其对应的包装对象，如 String、Number、Boolean
传递一个对象，函数中的 this 指向这个对象
```js
function a(){
    console.log(this); //输出函数a中的this对象
}
function b(){} //定义函数b
 
var obj = {name:'onepixel'}; //定义对象obj
 
a.call(); //window
a.call(null); //window
a.call(undefined);//window
a.call(1); //Number
a.call(''); //String
a.call(true); //Boolean
a.call(b);// function b(){}
a.call(obj); //Object
这是call 的核心功能，它允许你在一个对象上调用该对象没有定义的方法，并且这个方法可以访问该对象中的属性，至于这样做有什么好处，我待会再讲，我们先看一个简单的例子：
```
```js
var a = {
 
    name:'onepixel', //定义a的属性
 
    say:function(){ //定义a的方法
        console.log("Hi,I'm function a!");
    }
};
 
function b(name){
    console.log("Post params: "+ name);
    console.log("I'm "+ this.name);
    this.say();
}
 
b.call(a,'test');
>>
Post params: test
I'm onepixel
I'm function a!
```

当执行b.call 时，字符串`test`作为参数传递给了函数b,由于call的作用，函数b中的this指向了对象a, 因此相当于调用了对象a上的函数b,而实际上a中没有定义b 。

2、apply(thisArgs [,args[]])
apply 和 call 的唯一区别是第二个参数的传递方式不同，apply 的第二个参数必须是一个数组，而 call 允许传递一个参数列表。值得你注意的是，虽然 apply 接收的是一个参数数组，但在传递给调用函数时，却是以参数列表的形式传递，我们看个简单的例子：
```js
function b(x,y,z){
    console.log(x,y,z);
}
 
b.apply(null,[1,2,3]); // 1 2 3
```
apply 的这个特性很重要，我们会在下面的应用场景中提到这个特性。

3、bind(thisArgs [,args...])
bind是ES5 新增的一个方法，它的传参和call类似，但又和 call/apply 有着显著的不同，即调用 call 或 apply 都会自动执行对应的函数，而 bind 不会执行对应的函数，只是返回了对函数的引用。粗略一看，bind 似乎比call/apply 要落后一些，那ES5为什么还要引入bind 呢？

其实，ES5引入 bind 的真正目的是为了弥补 call/apply 的不足，由于 call/apply 会对目标函数自动执行，从而导致它无法在事件绑定函数中使用，因为事件绑定函数不需要我们手动执行，它是在事件被触发时由JS 内部自动执行的。而 bind 在实现改变函数 this 的同时又不会自动执行目标函数，因此可以完美的解决上述问题，看一个例子就能明白：
```js
var obj = {name:'onepixel'};
 
/**
 * 给document添加click事件监听，并绑定onClick函数
 * 通过bind方法设置onClick的this为obj，并传递参数p1,p2
 */
document.addEventListener('click',onClick.bind(obj,'p1','p2'),false);
 
//当点击网页时触发并执行
function onClick(a,b){
    console.log(
            this.name, //onepixel
            a, //p1
            b  //p2
    )
}
```
当点击网页时，onClick 被触发执行，输出onepixel p1 p2, 说明 onClick 中的 this 被 bind 改变成了obj 对象，为了对 bind 进行深入的理解，我们来看一下 bind 的 polyfill 实现：
```js
if (!Function.prototype.bind) {
    Function.prototype.bind = function (oThis) {
        var aArgs = Array.prototype.slice.call(arguments, 1),
            fToBind = this, // this在这里指向的是目标函数
            fBound = function () {
                return fToBind.apply(
                    // 如果外部执行var obj = new fBound(),则将obj作为最终的this，放弃使用oThis
                    this instanceof fToBind
                            ? this  // 此时的this就是new出的obj
                            : oThis || this, // 如果传递的oThis无效，就将fBound的调用者作为this
 
                    // 将通过bind传递的参数和调用时传递的参数进行合并，并作为最终的参数传递
                    aArgs.concat(Array.prototype.slice.call(arguments)));
            };
 
        // 将目标函数的原型对象拷贝到新函数中，因为目标函数有可能被当作构造函数使用
        fBound.prototype = this.prototype;
 
        // 返回fBond的引用，由外部按需调用
        return fBound;
    };
}
```

一旦函数通过bind传递了有效的this对象，则该函数在运行期的this将指向这个对象，即使通过call或apply来试图改变this的指向也是徒劳的。
```js
// 实现数组的去重功能
Array.prototype.unique = function(fn) {
        var rst = [];
        var tmp = {};
        this.forEach(function(val) {
            // 使用call来改变fn的this指向，这里传window
            var key = 'uniq' + (typeof fn === 'function' ? fn.call(window, val) : val);
            if (!tmp.hasOwnProperty(key)) {
                rst.push(val);
                tmp[key] = null;
            }
        }, this);
 
        return rst;
}
 
// 对象数组去重
var arr = [
    { id: 2 }, { id: 4 }, { id: 3 }, { id: 3 }, { id: 4 }, { id: 6 }
]
arr.unique(function(v) {
     console.log(this) // 使用bind传递了Array,则this一定是Array,而不会是window
     return v.id
}.bind(Array));
```
4、应用场景一：继承
大家知道，JavaScript中没有诸如Java、C# 等高级语言中的extend 关键字，因此JS 中没有继承的概念，如果一定要继承的话，call 和 apply 可以实现这个功能：
```js
function Animal(name,weight){
   this.name = name;
   this.weight = weight;
}
 
function Cat(){
    Animal.call(this,'cat','50');
  //Animal.apply(this,['cat','50']);
 
   this.say = function(){
      console.log("I am " + this.name+",my weight is " + this.weight);
   }
}
 
var cat = new Cat();
cat.say();//I am cat,my weight is 50
```
当通过new 运算符产生了cat 时，Cat中的 this 就指向了cat对象(关于new运算符的讲解，请参考JS构造函数和new运算符，而继承的关键是在于Cat中执行了Animal.call(this,'cat','50') 这句话，在call中将this作为thisArgs参数传递，于是Animal 方法中的 this 就指向了Cat中的 this，而 cat 中的 this 指向的是 cat 对象，所以Animal 中的 this 指向的就是 cat 对象，在 Animal 中定义了name 和 weight 属性，就相当于在 cat 中定义了这些属性，因此 cat 对象便拥有了Animal 中定义的属性，从而达到了继承的目的。

5、应用场景二：移花接木
在讲下面的内容之前，我们首先来认识一下JavaScript 中的一个非标准专业术语：ArrayLike (类数组/伪数组)

ArrayLike 对象即拥有数组的一部分行为，在DOM 中早已表现出来，而jQuery 的崛起让ArrayLike 在JavaScript 中大放异彩。ArrayLike 对象的精妙在于它和JS 原生的 Array 类似，但是它是自由构建的，它来自开发者对JavaScript 对象的扩展，也就是说：对于它的原型(prototype)我们可以自由定义，而不会污染到JS原生的Array。 

ArrayLike 对象在JS中被广泛使用，比如DOM 中的NodeList, 函数中的arguments 都是类数组对象，这些对象像数组一样存储着每一个元素，但它没有操作数组的方法，而我们可以通过call 将数组的某些方法`移接`到ArrayLike 对象，从而达到操作其元素的目的。比如我们可以这样遍历函数中的arguments:
```js
function test(){
    // 检测arguments是否为Array的实例
    console.log(
            arguments instanceof Array, //false
            Array.isArray(arguments)  //false
    );
    // 判断arguments是否有forEach方法
    console.log(arguments.forEach); //undefined
 
    // 将数组中的forEach应用到arguments上
    Array.prototype.forEach.call(arguments,function(item){
        console.log(item); // 1 2 3 4
    });
 
}
test(1,2,3,4);
```
除此之外，对于apply 而言，我们上面提到了它独有的一个特性，即apply 接收的是数组，在传递给调用函数的时候是以参数列表传递的。 这个特性让apply 看起来比call 略胜一筹，比如有这样一个场景：给定一个数组[1,3,4,7]，然后求数组中的最大元素，而我们知道，数组中并没有获取最大值的方法，一般情况下，你需要通过编写代码来实现。而我们知道，Math 对象中有一个获取最大值的方法，即Math.max()， max方法需要传递一个参数列表，然后返回这些参数中的最大值。而apply 不仅可以将Math 对象的max 方法应用到其他对象上，还可以将一个数组转化为参数列表传递给max,看代码就能一目了然：
```js
var arr = [2,3,1,5,4];
 
Math.max.apply(null,arr); // 5
```
以上便是 call 和 apply 比较经典的几个应用场景，熟练掌握这些技巧，并把这些特性应用到你的实际项目中，会使你的代码看起来更加耐人寻味！

[原文出处] @一像素  博客园 2016.01](https://www.cnblogs.com/onepixel/p/5143863.html) 
