在当今的前端主流框架中，都使用到了一种技术叫虚拟DOM，本文将讲解什么是虚拟DOM，如何实现一个简单的渲染虚拟DOM函数，以及虚拟DOM的优缺点。
# 什么是虚拟DOM？
虚拟 DOM (Virtual DOM，简称 VDOM) 是一种编程概念，意为将目标所需的 UI 通过数据结构“虚拟”地表示出来，保存在内存中，然后将真实的 DOM 与之保持同步。  

实际上它只是一层对真实DOM的抽象，以JavaScript 对象 (VNode 节点) 作为基础的树，用对象的属性来描述节点，最终可以通过一系列操作使这棵树映射到真实环境上  

在Javascript对象中，虚拟DOM 表现为一个 Object对象。并且最少包含标签名 (tag)、属性 (attrs) 和子元素对象 (children) 三个属性，不同框架对这三个属性的名命可能会有差别  
下面就给大家简单的实现一个虚拟DOM，以及实现虚拟DOM的渲染函数。  
# 实现虚拟DOM
下面先来看一段真实DOM
```javascript
<div class='red'>
  <span>hello</span>
</div>
```
这个真实DOM转化为虚拟DOM就是这样的
```javascript
const vdom = {
  tag:'div',
  attrs:{class:'red'},
  children:[
    {
      tag:'span',
      attrs:null,
      children:'hello'
    }
  ]
}
```
这里我们做了一个简化，children只有两种情况，一种是新的节点，一种是只有一段string。下面我们将实现一个渲染函数，让整个虚拟DOM挂载到页面上。
```javascript
//假如我们要挂载到这个节点上
<div id="app"></div>

mount(vdom , document.getElementById('app'))

//挂载函数
const mount=(vdom,container)=>{
  const el = document.createElement(vdom.tag)
  if(vdom.attrs){
    for(const key in vdom.attrs){
      const value=vdom.attrs[key]
      el.setAttribute(key,value)
    }
  }
  if(vdom.children){
    if(typeof vdom.children === 'string'){
      el.textContent = vdom.children
    }else{
      vdom.children.forEach((child)=>{
        mount(child,el)
      })
    }
  }
  container.appendChild(el)
}
```
这样就可以把虚拟DOM变为真实DOM并挂载到页面上了。
# 虚拟DOM解决了什么问题？
1. DOM 操作性能问题。通过虚拟 DOM 和 diff 算法减少不必要的 DOM 操作，保证性能不太差
2. DOM 操作不方便问题。以前各种 DOM API 要记，现在只需要重点记忆操作数据的API

# 虚拟DOM有哪些优点？
1. 为React等框架带来了跨平台的能力，因为虚拟DOM除了渲染为真实节点，还可以渲染为其他东西，比如服务器渲染等。
2. 让DOM操作的整体性能更好，能通过diff算法减少不必要的DOM操作

# 虚拟DOM有哪些缺点？
无法进行极致优化：   
虽然虚拟 DOM + 合理的优化，足以应对绝大部分应用的性能需求，但在一些性能要求极高的应用中虚拟 DOM 无法进行针对性的极致优化。