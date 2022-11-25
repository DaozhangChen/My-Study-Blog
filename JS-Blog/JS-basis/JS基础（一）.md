# var let const 的区别
* var  

var是ES5的声明变量的方式，作用域为方法作用域，也就是说，如果在一个函数中单独用var声明一个变量，那么这个变量在函数外部是用不了的，并且var还具有变量提升的特点，除此之外var可以多次声明同一个变量，不会报错。
* let

let是ES6的声明变量方式，作用域是块级作用域，也就是说在花括号内用let定义的变量，在花括号的上级作用域是访问不到的，并且没有变量提升，也不能多次声明同一个变量，会报错。
* const

const也是ES6声明变量的方式，特性与let相同，只是const定义的变量无法对其字面量进行修改，并且在使用const定义变量时，必须初始化变量，除此之外，如果const变量引用了一个对象，那么对这个应用对象内的属性进行修改是可以的。  
**一般情况下，能使用const尽量使用const，其次是let，能不用var尽量不用。**
# JS的数据类型有哪些
JS的数据类型分为简单类型和复杂类型
* 简单类型

String,Number,Boolean,Symbol,Bigint,null,undefined
* 复杂类型

Object  
注意：普通对象、数组、正则、函数等都属于Object。
# for-in和for-of的区别
for-in和for-of都是用来对对象进行遍历的，但是for-in适合普通对象进行遍历，for-in遍历的是index，而for-of**只能**对可迭代的对象进行遍历，遍历的是对象的值，也就是说普通的对象不能使用for-of。  
可以使用for-of的有Array、Map、Set、String等

# Set、Map、WeakSet和WeakMap的区别？
* Set
 1. Set之中的成员不能重复
 2. 只有键值，没有键名，类似数组的形式
 3. 可以遍历，方法有add、delete、has
* WeakSet

 1. 成员都是对象
 2. 成员都是弱引用，随时可以消失
 3. 不能遍历
* Map

 1. 本质上是键值对的集合，键和值可以是任意类型
 2. 可以遍历，键是有序的
* WeakMap

1. 只接受对象作为键名，不接受其他类型的值作为键名
2. 键名指向的对象，不计入垃圾回收机制
3. 不能遍历

# 关于内存泄露
内存泄露指的是当已经不需要某块内存时，这块内存还存在着，没有被释放或者说没有被垃圾回收，导致了内存占用越来越高，这样的现象就是内存泄露。  
在JS中有几种常见的内存泄露的情况  
1. 意外的全局变量

```javascript
function foo(arg) {
    bar = "this is a hidden global variable";
}
//或者由this创建的全局变量
function foo() {
    this.variable = "potential accidental global";
}
// foo 调用自己，this 指向了全局对象（window）
foo();
```
2. 不规范的使用闭包

```javascript
function bindEvent() {
  var obj = document.createElement('XXX');
  var unused = function () {
    console.log(obj, '闭包内引用obj obj不会被释放');
  };
  obj = null; // 解决方法
}
```
3. 未被清空的定时器

```javascript
var someResource = getData();
setInterval(function() {
    var node = document.getElementById('Node');
    if(node) {
        // 处理 node 和 someResource
        node.innerHTML = JSON.stringify(someResource);
    }
}, 1000);
```
4. 未被销毁的事件监听

```javascript
componentDidMount(){
    window.addEventListener("resize", this.onResize);
}
componentWillUnmount(){
  // 忘记remove EventListener
}
```
5. DOM引用

```javascript
const refA = document.getElementById('refA');
document.body.removeChild(refA); // dom删除了
console.log(refA, 'refA'); // 但是还存在引用能console出整个div 没有被回收
refA = null;
console.log(refA, 'refA'); // 解除引用
```
# 垃圾回收的机制
垃圾回收是JS中特殊的一个释放内存的方式，垃圾收集器会定期（周期性）的找出那些不再继续使用的变量，然后释放内存。  
通常有两种实现的方式：
* 标记清除
* 引用计数

### 标记清除
标记清除是JS中**最常用**的垃圾回收策略，当变量进入上下文，比如在函数内部声明一个变量是，这个变量会被加上存在于上下文中的标记，当变量离开上下文时，也会被加上离开上下文的标记。  
在垃圾回收程序运行时，会标记内存中储存的所有变量，然后会将处在上下文中的变量以及上下文引用的变量的标记去掉，再次之后再被加上标记的变量就是待删除的，随后垃圾回收程序做一次内存清理，销毁带标记的所有值并且回收他们的内存。
```javascript
var m = 0,n = 19 // 把 m,n,add() 标记为进入环境。
add(m, n) // 把 a, b, c标记为进入环境。
console.log(n) // a,b,c标记为离开环境，等待垃圾回收。
function add(a, b) {
  a++
  var c = a + b
  return c
}
```
### 引用计数
语言引擎有一张"引用表"，保存了内存里面所有的资源（通常是各种值）的引用次数。如果一个值的引用次数是0，就表示这个值不再用到了，因此可以将这块内存释放

如果一个值不再需要了，引用数却不为0，垃圾回收机制无法释放这块内存，从而导致内存泄漏
```javascript
const arr = [1, 2, 3, 4];
```
上面代码中，数组[1, 2, 3, 4]是一个值，会占用内存。变量arr是仅有的对这个值的引用，因此引用次数为1。尽管后面的代码没有用到arr，它还是会持续占用内存

如果需要这块内存被垃圾回收机制释放，只需要设置如下：
```javascript
arr = null
```
通过设置arr为null，就解除了对数组[1,2,3,4]的引用，引用次数变为 0，就被垃圾回收了  