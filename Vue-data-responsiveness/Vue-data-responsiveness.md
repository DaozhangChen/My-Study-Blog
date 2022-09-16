# Vue2数据响应式  
什么是响应式？响应式就是一个物体能对外界的变化做出反应，那它就是响应式的，那么在Vue中，我们如果修改Vue中的n数据，那么UI中的n就会发生反应，这就是Vue的数据响应式，在Vue2中主要通过Object.defineProperty来实现数据的响应式。   
## Obj.defineProperty()
Object.defineProperty() 方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回此对象。  
这个方法的语法为：  
```javascript
Object.defineProperty(obj, prop, descriptor)

//obj  要定义属性的对象。

//prop 要定义或修改的属性的名称或 Symbol 。

//descriptor 要定义或修改的属性描述符
```  

一般在这个方法中会配合getter和setter方法一起使用，就可以做到数据响应式。  
我们先创建一个data对象，里面有一个n:0
```javascript
data:{n:0}
```
然后把这个对象放进一个自己定义的函数里，目的是为了将这个数据的修改代理给另一个变量
```javascript
let data3 = proxy({ data:{n:0} })
```
下面我们这么定义proxy函数
```javascript
function proxy({data}){
  const obj = {}
  Object.defineProperty(obj, 'n', { 
    get(){
      return data.n
    },
    set(value){
      if(value<0)return  //如果设置的数值小于0，原数值不变
      data.n = value
    }
  })
  return obj // obj 就是代理
}

data3.n = -1
console.log(`${data3.n}，设置为 -1 失败`)  //0
data3.n = 1
console.log(`${data3.n}，设置为 1 成功`)   //1
```
但是这样会有原数据被人篡改的风险，也就是当data:{n:0}暴露在外时，他人可以直接从这里对数据进行修改，不够安全。  
我们可以通过加入一个对原数据的监听，来避免这一点：  
```javascript
let myData5 = {n:0}
let data5 = proxy2({ data:myData5 })

function proxy2({data}){
  let value = data.n  //这里让原数据的值寄存在这里
  Object.defineProperty(data, 'n', {  //在原数据里监听n数据，此数据会直接覆盖掉原数据里的n
    get(){
      return value
    },
    set(newValue){
      if(newValue<0)return
      value = newValue
    }
  })
  // 就加了上面几句，这几句话会监听 data

  const obj = {}
  Object.defineProperty(obj, 'n', {
    get(){
      return data.n
    },
    set(value){
      data.n = value  //当上面的value改变时，原数据中的n也会被改变
    }
  })
  
  return obj  //返回一个obj对象，里面有跟原数据数值相同的数据
}

myData5.n = -1
console.log(`${data5.n}，设置为 -1 失败了`)  //0
myData5.n = 1
console.log(`${data5.n}，设置为 1 成功了`)   //1
```
上面的代码就是Vue2响应式数据的思想，但并非源代码，总之Vue2做到数据响应式的方式就是通过Object.defineProperty方法，对原数据进行代理以及监听，最后通过getter和Setter来判断修改的数据是否符合需要的标准。