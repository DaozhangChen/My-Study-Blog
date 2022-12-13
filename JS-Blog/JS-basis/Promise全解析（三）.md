# Promise.all()与Promise.race()
Promise类提供了两个将多个Promise合成为一个Promise的方法，新合成的Promise的状态取决于内部Promise的状态。  
## Promise.all()
Promise.all()的核心思想是，all内部所有的Promise只要有一个是被拒绝的（rejected），那么整个Promise.all()就是被拒绝的，只有内部所有Promise都成功了，才会使Promise.all()处于兑现（resolved）状态。  
Promise.all()接受一个可迭代对象，通常来说是一个数组，Promise.all()兑现时会返回参数数组中对应Promise的值。  
```javascript
let p1 = Promise.all([Promise.resolve(1),Promise.resolve(2)])
let p2 = Promise.all([3,4])
let p3 = Promise.all([])

setTimeout(console.log,0,p1)  //Promise {<fulfilled>: [1,2] }
setTimeout(console.log,0,p2)  //Promise {<fulfilled>: [3,4] }
setTimeout(console.log,0,p3)  //Promise {<fulfilled>: [] }
```
> 如果all其中的Promise的兑现时间不同，那么Promise.all()会等内部所有的Promise都完成后才会确定状态  


如果其中有一个Promise是被拒绝的，那么Promise.all()也会呈现被拒绝的状态，并且只要出现一个Promise是被拒绝的，那么Promise.all()的状态也就在这时确定了，使用catch等错误处理可以捕获到错误的原因。
```javascript
let p1 = Promise.all([Promise.resolve(1),Promise.reject(2)])
                .catch((res)=>console.log(res))
let p2 = Promise.all([new Promise((resove,reject)=>{setTimeout(()=>resove(3),100)}),
                      Promise.reject(4)])
                .catch((res)=>console.log(res))

//控制台打印2和4
```
## Promise.race()
Promise.race()与Promise.all()的概念相反，它是一组Promise中最先解决或拒绝的Promise的镜像，这个方法接受一个可迭代对象，通常也是数组，返回一个新Promise。  
Promise.race()不会对解决或拒绝的Promise区别对待，只要是第一个落定的Promise，Promise.race()就会包装其解决值或拒绝理由并返回新Promise。    
在race中，迭代顺序决定了Promise的落定顺序。  
```javascript
let p1 = Promise.race([Promise.resolve(1),Promise.resolve(2)])
let p2 = Promise.race([Promise.reject(3),Promise.reject(4)])

setTimeout(console.log,0,p1)  //Promise {<fulfilled>: 1}
setTimeout(console.log,0,p2)  //Promise {<rejected>: 3}
```
当状态已落定，那么后续Promise中无论是resolve还是reject都会被忽略，后续的Promise中**处于异步函数内的部分会被执行，处于同步函数内的代码不会被执行**。
```javascript
let p1 = new Promise(()=>{ console.log('hello');
                          setTimeout(()=>{console.log('world');
                                          Promise.reject()},500) })
let p2 = new Promise(()=>{ console.log('good');
                          setTimeout(()=>{console.log('bye');
                                          Promise.resolve()},1000) })

let p11 = Promise.race([Promise.resolve(11),p1])
let p21 = Promise.race([Promise.reject(21),p2])

setTimeout(console.log,0,p11)Promise {<fulfilled>: 11}
setTimeout(console.log,0,p21)Promise {<rejected>: 21}
//大约500ms后控制台打印       world
//大约1000ms后控制台继续打印  bye
```
