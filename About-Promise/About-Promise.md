# 异步与Promise
## 什么是同步？什么是异步？
我们知道，JavaScript是一个单线程的语言，也就是说，同一时间只能做一件事，JS的单线程为JS操作DOM提供了便利性，由于是单线程的，就不会存在同时增加与删除同一个DOM这样令人难以理解的操作。 

那么在JS所有的任务中，就有同步任务与异步任务，同步任务指代码一条一条有顺序的执行，上一个代码执行完后，才会执行下一个代码；而异步任务就是先把代码放一边去等待，先执行其他的代码，等到某一个任务点，可以执行这条代码后，再执行这条代码，这就是异步任务。那什么时候才能轮到异步任务执行呢，有两种方式，一个叫轮询，一个叫回调，这两个词的解释用一个餐厅吃饭的比喻来说明。  

比如你去餐厅吃饭，需要排队，然后你取了一个号，开始去其他地方逛街，那么你什么时候知道你可以吃饭了呢？一个是你可以每10分钟回到餐厅去问一下，这就是轮询，你也可以用微信扫码来接收通知，等到他通知你了，再回去吃饭，这就是回调。
  
## 异步和回调的关系
关联：  
* 异步任务需要在得到结果时通知JS来拿结果，如何通知？
* 可以让JS先留一个函数地址给浏览器，如同餐厅吃饭给服务员电话号码，等待服务员通知用餐
* 异步任务完成时浏览器调用该函数地址（拨打电话）
* 同时把结果作为参数传给该函数（电话里说可以用餐）
* 这个函数是写给浏览器调用的，并非自己主动调用，所以是回调函数  

区别：  
* 异步任务需要用到回调函数来通知结果
* 但回调函数不一定只用在异步任务里，还可以用到同步任务里

## 为什么要用到Promise
有一个摇骰子的例子：
```javascript
function 摇骰子(){
setTimeout(()=>{ 
return parseInt(Math.random() * 6) + 1
},1000)
// return undefined
}
```
如果代码是这样，那么将会得不到异步的结果，返回结果是undefined，那么如何得到他的结果呢？这时就需要一个回调函数fn：  
```javascript
function f1(x){ console.log(x) }
摇骰子(f1)

function 摇骰子(fn){
setTimeout(()=>{
fn(parseInt(Math.random() * 6) + 1)
},1000)
}
```
这样就可以吧异步的结果作为参数传递给回调函数，就可以得到异步函数的值了。  
但是异步任务有两个结果成功或失败，该怎么办？  
这时候一些聪明的前端就采用了接受两个参数或者两个回调的方法，但由于还是存在着诸多问题：  
* 命名不规范，有的人用success+error，有人用yes+no
* 容易出现回调地狱，代码变得难以理解
* 很难进行错误处理  

**这时候Promise就很好地解决了这个问题，它规范了回调的名字和顺序，提高了代码的可读性，能够很方便的捕获错误，于是Promise就被众前端采用了。**
## Promise的用法
如何使用Promise？  
可以用new Promise((resolve,reject)=>{})来新建一个Promise对象，或者在回调函数中用return new Promise((resolve,reject)=>{})来返回一个Promise对象。  
Promise接受两个函数，resolve是成功是调用的函数，reject是失败时调用的函数，两个函数的名字可以随便起。  
一个Promise必然会处于以下几种状态之一：  
* 待定：初始状态，没有被兑现，也没有被拒绝
* 已兑现：意味着操作成功完成
* 已拒绝：意味着失败   

Promise还可以采用链式调用：  
```javascript
Promise.prototype.then()
Promise.prototype.catch()
Promise.prototype.finally()
```
.then方法接受两个函数，也是成功时调用成功函数，失败时调用失败函数
```javascript
p.then(value => {
  // fulfillment
}, reason => {
  // rejection
})
```
catch方法可以捕获Promise的错误，只接受一个函数，其效果与`obj.then(undefined, onRejected)`相同。
```javascript
p.catch(onRejected);
//或
p.catch(function(reason) {
   // 拒绝
});
```
finally方法会在不论是成功还是失败中都会执行，也只接受一个函数：  
```javascript
p.finally(onFinally);
//或
p.finally(function() {
   // 返回状态为 (resolved 或 rejected)
});
```
