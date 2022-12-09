# 什么是Promise
Promise在JS这门语言中占领了一个极其重要的地位，他是JS解决异步编程的一个方案。  
Promise是一个类，Promise对象用于表示一个异步操作的最终完成（或失败）及其结果值。    
Promise解决了之前对于异步操作可能会导致的地狱回调的问题，并把这种嵌套回调的方式，修改为了链式的调用方式，降低了编码难度，使代码可读性明显增强。  
## Promise的状态  
一个Promise对象存在三种状态  
* 待定（pending）
* 兑现（fulfilled）
* 拒绝（rejected）  


待定状态时一个Promise对象最初的状态，比如创建一个Promise，然后直接在控制台打印出它，就会得到一个pending状态的Promise  
```javascript
let promise = new Promise(()=>{})
console.log(promise)             //Promise {<pending>}
```
处于待定态的Promise可以转变为两种状态，一种状态是兑现（fulfilled），一种状态是拒绝（rejected），Promise一旦改变成为其中一种状态（fulfilled或rejected）后，那么整个Promise的状态就不能被改变。   
由于Promise的状态是私有的，也就是说，Promise控制状态只能在Promise内部，外部是完全访问不到的。其中控制状态是通过Promise的两个函数参数来实现的，Promise函数内部调用resolve()可以改变状态为兑现（fulfilled），调用reject()可以改变状态为拒绝（rejected）并且**会抛出一个错误**，如下所示。  
```javascript
let p1 = new Promise((resolve,reject)=>{ resolve() })
let p2 = new Promise((resolve,reject)=>{ reject() })

console.log(p1)  //Promise {<fulfilled> : undefined }
console.log(p2)  //Promise {<rejected> : undefined }    Uncaught (in promise) undefined
```
这两个例子中没有异步操作，在p1,p2创建的时候，他们的状态就已经确定了，原因是resolve和reject函数是**同步执行**的  
如果在调用resolve后，再次调用reject，那么reject是无效的，因为Promise状态一旦改变就无法再次变更。反之先调用reject后调用resolve也是同理。  
```javascript
let p1 = new Promise((resolve,reject)=>{ 
    resolve();
    reject();
})
console.log(p1)   //Promise {<fulfilled>: undefined}
```
## Promise.resolve()
Promise在一开始不一定需要处于待定（pending）状态，通过调用Promise.resolve()可以实例化一个已经被兑现的Promise，rejected也是如此。
```javascript
let p1=Promise.resolve()
let p2=Promise.reject()

console.log(p1)    //Promise {<fulfilled>: undefined}
console.log(p2)    //Promise {<rejected>: undefined}
```
Promise.resolve()方法接受一个参数，参数有且仅有一个，多余的参数会被忽略，若是没有参数则默认为undefined
```javascript
let p1=Promise.resolve()
let p2=Promise.resolve(3,4,5)    //多余的参数会被忽略

console.log(p1)    //Promise {<fulfilled>: undefined}
console.log(p2)    //Promise {<rejected>: 3}
```
这个参数会随着resolve方法传递给后续的处理程序，并且这种传递是**幂等性**的，也就是说，不管外部嵌套了多少层resolve，他们传递的值都是一样的。如下所示
```javascript
let p1 = Promise.resolve(1)
let p2 = Promise.resolve(p1)

console.log(p1)        //Promise {<fulfilled>: 1}
console.log(p2)        //Promise {<fulfilled>: 1}
console.log(p1===p2)   //true
```
并且resolve中可以传递任何参数，有时候会导致一些奇怪的事。。
```javascript
let p1=Promise.resolve(new Error('Promise Error'))
console.log(p1)          //Promise {<fulfilled>: Error: Promise Error}  

//浏览器没有抛出错误提示
```
## Promise.reject()
Promise.reject()与resolve类似，可以接受一个参数传递给后续的处理程序，并且也可以直接调用创造一个被拒绝的Promise（上面说过了），但是有一点不同，那就是reject()的传参**不具有幂等性**。如果reject包裹的参数是一个Promise，那么当前的Promise就会以这个被包裹住的Promise作为拒绝的理由，而不会持续传参。
```javascript
let p1 = Promise.resolve(1)
let p2 = Promise.reject(p1)

console.log(p2)           //Promise {<rejected>: Promise}
```
还有一点需要注意的，就是reject中的参数信息不可以通过try/catch捕获到，而只能通过Promise本身的Promise.prototype.catch捕获。原因是try/catch只能捕获到同步的错误，它无法捕获到异步中的错误。
