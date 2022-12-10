# Promise的实例方法
Promise的实例方法是连接外部同步代码和内部异步代码之间的桥梁，这些方法可以根据异步操作返回的数据在处理成功或失败的结果。  
## Promise.prototype.then()
then()是Promise处理程序的主要方法，它最多接受两个参数，第一个参数是对应上一个Promise为**兑现（fulfilled）**的处理方法，第二个参数是对**rejected**的处理方法  
then方法会将上一个Promise的结果作为自己的参数使用，若上一个Promise的状态为resolve，那么then的第一个回调函数就拥有一个参数，即上一个Promise的结果，第二个回调函数也相同，它接受上一个被拒绝的Promise的结果当做参数  
```javascript
let p1=new Promise((resolve,reject)=>resolve('resolved promise'))
let p2=new Promise((resolve,reject)=>reject('rejected promise'))

let p11=p1.then((data)=>console.log(data))

let p21=p2.then(null,(res)=>console.log(res))

setTimeout(console.log,0,p11)  //resolved promise
setTimeout(console.log,0,p21)  //rejected promise
```
当上一个Promise结果为兑现（fulfilled）时就会调用then的第一个参数的回调函数，上一个Promise结果为拒绝时，就会调用then的第二个参数作为回调函数。  
```javascript
let p1=new Promise((resolve,reject)=>resolve())
let p2=new Promise((resolve,reject)=>reject())

let p3=p1.then(()=>{console.log('p1兑现了')},()=>{console.log('p1失败了')})
let p4=p2.then(()=>{console.log('p2兑现了')},()=>{console.log('p2失败了')})

//p1兑现了
//p2失败了
```
then()也会返回一个新的Promise，这个Promise的状态是根据处理函数的返回值来构建的，如果返回值是Promise，那么会根据这个返回的Promise状态来确定返回值的状态，如果不是Promise，就会默认以Promise.resolve()来包装这个值。  
```javascript
//返回值为Promise
let p1=new Promise((resolve,reject)=>resolve())
let p2=new Promise((resolve,reject)=>reject())
let p3=p1.then(()=>Promise.reject(),()=>Promise.resolve())
let p4=p2.then(()=>Promise.reject(),()=>Promise.resolve())

setTimeout(console.log,0,p3)    //Promise {<rejected>: undefined}
setTimeout(console.log,0,p4)    //Promise {<fulfilled>: undefined}

//返回值不为Promise
let p1=new Promise((resolve,reject)=>resolve())
let p2=new Promise((resolve,reject)=>reject())

let p11=p1.then(()=>'bar')
let p12=p1.then(()=> 1 )
let p13=p1.then(()=>{})

let p21=p2.then(null,()=>'bar')
let p22=p2.then(null,()=> 1 )
let p23=p2.then(null,()=>{})

setTimeout(console.log,0,p11)  //Promise {<fulfilled>: 'bar'}
setTimeout(console.log,0,p12)  //Promise {<fulfilled>: 1}
setTimeout(console.log,0,p13)  //Promise {<fulfilled>: undefined}
setTimeout(console.log,0,p21)  //Promise {<fulfilled>: 'bar'}
setTimeout(console.log,0,p22)  //Promise {<fulfilled>: 1}
setTimeout(console.log,0,p23)  //Promise {<fulfilled>: undefined}
```
值得注意的是，当返回值为throw与new Error这些错误值时，会有些许不同。  
throw会把Promise的状态变为rejected，而Error却不会，仍然保持resolve。  
```javascript
let p1=new Promise((resolve,reject)=>resolve())

let p11=p1.then(()=>{throw 'throw error'})
let p12=p1.then(()=> Error('new Error') )

setTimeout(console.log,0,p11)  //Promise {<rejected>: 'throw error'}
setTimeout(console.log,0,p12)  //Promise {<fulfilled>: Error: new Error}
```  
## Promise.prototype.catch()  
catch方法用于给Promise添加拒绝处理程序，只接受一个参数，就是如何处理错误，这个方法是一个语法糖，调用它等价于调用then的第二个参数的回调函数。  
catch也会返回一个新的Promise，返回的Promise的状态与then方法返回的状态是一致的。  
```javascript
//catch返回新的Promise
let p1=new Promise(()=>{})
let p2=p1.catch()

setTimeout(console.log,0,p1)        //Promise {<pending>}
setTimeout(console.log,0,p2)        //Promise {<pending>}
setTimeout(console.log,0,p1===p2)   //false
```
## Promise.prototype.finally()
finally方法用于给Promise添加最终的处理程序，不论之前的Promise是成功或是失败都会执行  
```javascript
let p1=Promise.resolve()
let p2=Promise.reject()

let p11=p1.finally(()=>console.log('p1 finally'))  //p1 finally
let p21=p2.finally(()=>console.log('p2 finally'))  //p2 finally
```
finally也会返回一个全新的Promise，但是与then和catch不同的是，finally返回的Promise在大多数情况下表现为父Promise的传递，当finally返回的是一个被拒绝的Promise，或者是throw了一个错误时，就会返回这个被拒绝的Promise  
```javascript
let p1=Promise.resolve('p1 resolved')
let p2=Promise.reject('p2 rejected')

let p11=p1.finally(()=>'p11')
let p12=p1.finally(()=>Promise.resolve('p12 resolved'))
let p13=p1.finally(()=>undefined)
let p14=p1.finally(()=>{throw 'p14 error'})
let p21=p2.finally(()=>'p21')
let p22=p2.finally(()=>Promise.reject('p22 rejected'))
let p23=p2.finally(()=>undefined)
let p24=p1.finally(()=>{throw 'p14 error'})


setTimeout(console.log,0,p11)  //Promise {<fulfilled>: 'p1 resolved'}
setTimeout(console.log,0,p12)  //Promise {<fulfilled>: 'p1 resolved'}
setTimeout(console.log,0,p13)  //Promise {<fulfilled>: 'p1 resolved'}
setTimeout(console.log,0,p14)  //Promise {<rejected>: 'p14 error'}
setTimeout(console.log,0,p21)  //Promise {<rejected>: 'p2 rejected'}
setTimeout(console.log,0,p22)  //Promise {<rejected>: 'p22 rejected'}
setTimeout(console.log,0,p23)  //Promise {<rejected>: 'p2 rejected'}
setTimeout(console.log,0,p24)  //Promise {<rejected>: 'p14 error'}
```
还有一种情况会返回一个待定态的Promise，就是当返回值是一个new Promise并且这个Promise没有完成时，就会返回一个待定态的Promise，当然，如果这个Promise只是暂时没有完成，但终究会完成的话，那么就会按照上面的情况进行处理。  
```javascript
let p1=Promise.resolve('p1 resolved')
let p11=p1.finally(()=>new Promise(()=>{}))
let p12=p1.finally(()=>new Promise((resolve,reject)=>{setTimeout(()=>resolve('p12 resolved'),500)}))
let p13=p1.finally(()=>new Promise((resolve,reject)=>{setTimeout(()=>reject('p13 rejected'),500)}))

setTimeout(console.log,0,p11)  //Promise {<pending>}
setTimeout(console.log,0,p12)  //Promise {<pending>}
setTimeout(console.log,0,p13)  //Promise {<pending>}

//大约1s后
setTimeout(console.log,1000,p12)  //Promise {<fulfilled>: 'p1 resolved'}
setTimeout(console.log,1000,p13)  //Promise {<rejected>: 'p13 rejected'}
```
# Promise代码的执行顺序
Promise中的执行顺序是由讲究的，当生成一个new Promise时，这个新的Promise内部的代码是同步执行的，而这个Promise的then方法一定是之前的Promise完成后才会执行，并且，如果同时外部有一个同步代码同时执行，那么then方法的执行时间一定会在同步代码之后。    
并且，如果在代码排列顺序上先执行了then方法，也会是一样的结果，因为then方法一定需要等之前Promise的同步代码以及其他的同步代码完成后，才会执行。  
```javascript
let p1=new Promise((resolve)=>{
    func = function(){
        console.log('1')
        resolve()
        console.log('2')
    }
})
let p2=p1.then(()=>{console.log( '3' )})
func()
console.log('4')

//1
//2
//4
//3
```
并且，同级的处理程序会按级别依次进行，无论是then，catch还是finally都是如此  
```javascript
let p1=Promise.resolve()
let p2=Promise.reject()

let p3=p1.then(()=>console.log('1'))
         .then(()=>console.log('2'))
         .finally(()=>console.log('3'))

let p4=p2.then(null,()=>{console.log('11');throw 'error'})
         .catch(()=>console.log('12'))
         .finally(()=>console.log('13'))

//1
//11
//2
//12
//3
//13
```
