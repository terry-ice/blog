## array 是啥玩意儿？
### JavaScript中对数组的定义
数组的标准定义是:一个存储元素的线性集合(collection)，元素可以通过索引来任意存 取，索引通常是数字，用来计算元素之间存储位置的偏移量。几乎所有的编程语言都有类 似的数据结构。然而 JavaScript 的数组却略有不同。
JavaScript 中的数组是一种特殊的对象，用来表示偏移量的索引是该对象的属性，索引可 能是整数。然而，这些数字索引在内部被转换为字符串类型，这是因为 JavaScript 对象中 的属性名必须是字符串。数组在 JavaScript 中只是一种特殊的对象，所以效率上不如其他 语言中的数组高。
JavaScript 中的数组，严格来说应该称作对象，是特殊的 JavaScript 对象，在内部被归类为数 组。由于 Array 在 JavaScript 中被当作对象，因此它有许多属性和方法可以在编程时使用。
### 使用数组
```js
var numbers = []; //将会创建一个长度为0 的空数组
console.log(numbers.length); // 显示 0

var numbers = [1,2,3,4,5]; //直接在 [] 操作符内放入一组元素:
console.log(numbers.length); // 显示 5

var numbers = new Array(); // Array 的构造函数创建数组
console.log(numbers.length); // 显示 0

var numbers = new Array(1,2,3,4,5); //可以为构造函数传入一组元素作为数组的初始值:
console.log(numbers.length); // 显示 5

//在调用 Array 的构造函数时，可以只传入一个参数，用来指定数组的长度
var numbers = new Array(10);
console.log(numbers.length); // 显示 10

//数组中的元素有多种数据类型
var objects = [1, "Joe", true, null];

//可以通过Array.isArray() 判断是否是数组
var numbers = 3;
var arr = [7,4,1776]; 
console.log(Array.isArray(numbers)); // 显示 false
console.log(Array.isArray(arr)); // 显示 true
```
### 数组的方法
#### 查找元素indexOf(),lastIndexOf()
indexOf() 函数是最常用的存取函数之一，用来查找传进来的参数在目标数组中是否存在。 如果目标数组包含该参数，就返回该元素在数组中的索引;如果不包含，就返回 -1。
```js
var names = ["David", "Cynthia", "Raymond", "Clayton", "Jennifer"];
var name  ="terry"
var position = names.indexOf(name);
if (position >= 0) {
  console.log("Found " + name + " at position " + position);
} else {
  console.log(name + " not found in array.");
}
```
如果数组中包含多个相同的元素，indexOf() 函数总是返回第一个与参数相同的元素的索 引。有另外一个功能与之类似的函数:lastIndexOf()，该函数返回相同元素中最后一个元 素的索引，如果没找到相同元素，则返回 -1。
```js
var names = ["David", "Mike", "Cynthia", "Raymond", "Clayton", "Mike", "Jennifer"];
var name = "Mike";
var firstPos = names.indexOf(name);
console.log("First found " + name + " at position " + firstPos);
var lastPos = names.lastIndexOf(name);
console("Last found " + name + " at position " + lastPos);
```
#### 数组的一些操作方法
##### 添加
数组添加元素:push() 和 unshift()
push() 方法会将一个元素添加到数组末尾:
```js
var nums = [1,2,3,4,5];
console.log(nums); // 1,2,3,4,5
nums.push(6);
console.log(nums); // 1,2,3,4,5,6

var nums2 = [2,3,4,5];
console.log(nums2); // 2,3,4,5
var newnum = 1;
nums.unshift(newnum);
console.log(nums2); // 1,2,3,4,5
```
##### 删除
使用 pop() 方法可以删除数组末尾的元素，shift() 方法可以删除数组的第一个元素
```js
var nums = [1,2,3,4,5,9];
nums.pop();
console.log(nums); // 1,2,3,4,5

var nums = [9,1,2,3,4,5];
nums.shift();
console.log(nums); // 1,2,3,4,5
```

