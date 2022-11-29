在Vue3中对于数据响应式采用了ES6的Proxy的方法，从而代替了Vue2中实现响应式的defineProperty方法，来看一下是为什么？
# Object.defineProperty
Vue2实现响应式的原理，是通过Object.defineProperty的getter和setter
下面实现一个简单的响应式函数
```javascript
function update() {
    app.innerText = obj.foo
}

function defineReactive(obj, key, val) {
    Object.defineProperty(obj, key, {
        get() {
            return val
        },
        set(newVal) {
            if (newVal !== val) {
                val = newVal
                update()
            }
        }
    })
}
```
这样我们调用defineReactive，绑定一个对象，那么对这个对象中的某个key改变value，就会触发这里的setter，setter中就会调用update方法，去改变UI视图。
```javascript
const obj = {}
defineReactive(obj, 'foo', '')
setTimeout(()=>{
    obj.foo = new Date().toLocaleTimeString()
},1000)
```
如果对象中存在多个key值，就必须对这个对象进行遍历，使每个key都变成响应式的
```javascript
function observe(obj) {
    if (typeof obj !== 'object' || obj == null) {
        return
    }
    Object.keys(obj).forEach(key => {
        defineReactive(obj, key, obj[key])
    })
}
```
如果是嵌套对象，还需要进行递归，使内部的数据也变成响应式的，如果key赋值的是对象，那么也需要对这个对象进行响应式化。
```javascript
function defineReactive(obj, key, val) {
    observe(val)
    Object.defineProperty(obj, key, {
        get() {
            return val
        },
        set(newVal) {
            if (newVal !== val) {
               observe(newVal) //新值为对象的情况
                val = newVal
                update()
            }
        }
    })
}
```
这样就完成了一个对象的简单响应式。  
但是这样我们会发现对一个对象的删除，添加方法就不起作用了
```javascript
const obj = {
    foo: "foo",
    bar: "bar"
}
observe(obj)
delete obj.foo // 不起作用
obj.jar = 'xxx' // 不起作用
```
并且对一个数组进行监听后，某些数组的方法也不能用了
```javascript
const arrData = [1,2,3,4,5];
arrData.forEach((val,index)=>{
    defineProperty(arrData,index,val)
})
arrData.push() // 没有用
arrData.pop()  // 没有用
arrDate[0] = 99 // 这是可以的
```
可以看到数据的api无法被劫持到，从而无法实现数据的响应式，所以在vue2中增加了set、delete等API，并且对数组的api也进行了重写。并且这种响应式对深层的嵌套对象，需要进行深层的监听，会消耗极大的性能。
# Proxy
使用ES6的Proxy就可以针对对象的所有操作进行监听，就可以代理所有属性了。  
实现一个简单的reactive方法
```javascript
function reactive(obj) {
    if (typeof obj !== 'object' && obj != null) {
        return obj
    }
    // Proxy相当于在对象外层加拦截
    const observed = new Proxy(obj, {
        get(target, key, receiver) {
            const res = Reflect.get(target, key, receiver)
            return res
        },
        set(target, key, value, receiver) {
            const res = Reflect.set(target, key, value, receiver)
            return res
        },
        deleteProperty(target, key) {
            const res = Reflect.deleteProperty(target, key)
            return res
        }
    })
    return observed
}
```
这样就实现了一个简单对象的reactive方法，但是这样对于获取嵌套对象的属性会有一些小问题
```javascript
const state = reactive({
    bar: { a: 1 }
})

// 设置嵌套对象属性
state.bar.a = 10 // no ok
```
这样就需要在get中对返回的对象进行判断
```javascript
function reactive(obj) {
    if (typeof obj !== 'object' && obj != null) {
        return obj
    }
    // Proxy相当于在对象外层加拦截
    const observed = new Proxy(obj, {
        get(target, key, receiver) {
            const res = Reflect.get(target, key, receiver)
            return isObject(res) ? reactive(res) : res
        },
    return observed
    })
}
```
# 总结
总之，Vue3使用Proxy代替defineProperty的原因有：  
1. Proxy能对整个对象的任何方法进行劫持，而defineProperty不能做到这些，需要对某些操作对象和数组的API进行重写才行。
2. defineProperty不能检测到对象属性的添加和删除，需要写一个专门的API来做这些事情
3. Proxy对性能的消耗比defineProperty要小
