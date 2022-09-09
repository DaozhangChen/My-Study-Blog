# 浅析MVC
1. MVC三个对象分别做什么？
2. EventBus有哪些API，是做什么用的？
3. 什么是表驱动编程？
4. 如何理解模块化

## MVC三个对象分别做什么
MVC是一种架构设计模式，它通过关注点分离鼓励改进应用程序组织，说白了，就是一种写代码的思想，能够帮助开发者写出更加“规范”的代码，MVC中包括了三类对象，分别对应M，V，C三个字母，分别为：  
* Model模型
* View视图
* Controller控制器  
  
模型是用于封装应用程序的业务逻辑相关的数据以及对数据的处理方法，视图就是用户所看到的界面，控制器就是定义用户界面对用户输入的响应方式，起到不同层面的组织作用，用于控制应用程序的流程，他处理用户的行为和数据model上的改变。

举个例子说明，比如有一个计算机页面，用户可以通过加减乘除来控制数字的大小，那么这里的m就是控制计算机的计算逻辑，以及数字的反馈，这里的v就是计算机显示给用户的数字，以及加减乘除的按钮，c就是来监听点击事件，并将点击事件传送给运算模块，最后能够成功反馈需要显示的数字。  

总之，MVC就是一种设计模式，能够很好的完成编程的工程化实现。随着时代的发展，还有更多由MVC启发的设计模式，如MVP、MVVM、MV*等，这些框架都显示着设计模式的重要性。
## EventBus有哪些API，是做什么用的？
EventBus能够简化各组件间的通信，让我们的代码书写变得简单，能有效的分离事件发送方和接收方(也就是解耦的意思)，能避免复杂和容易出错的依赖性和生命周期问题。EventBus基本的api有on、trigger、off，分别用于监听事件，触发事件，取消监听。
```javascript
//EventBus.js
class EventBus{
    constructor(){
        this._eventBus =$(window)
    }
    on(eventName, fn){
        return this._eventBus.on(eventName,fn)
    }
    trigger(eventName,data){
        return this._trigger.tiggger(eventName,data)
    }
    off(eventName, fn){
        return this._eventBus.off(eventName,fn)
    }
}
export default EventBus
//new.js
import EventBus from 'EventBus.js'
const e =new EventBus()
e.on()
e.trigger()
e.off()
```
## 什么是表驱动编程？
《代码大全》对表驱动编程的描述：  
表驱动方法是一种使你可以在表中查找信息，而不必用逻辑语句（if 或 case）来把他们找出来的方法。事实上，任何信息都可以通过表来挑选。在简单的情况下，逻辑语句往往更简单而且更直接。但随着逻辑链的复杂，表就变得越来越富于吸引力了。  
表驱动编程的意义在于逻辑与数据的分离。
```javascript
function translate(term) {
    let terms = {
        '1': '一',
        '2': '二',
        '3': '三'
    }
    return terms[term];
}

// 如果想添加一个新的名词翻译，只需要在terms中添加一个新的表项，不需要修改整个逻辑
function translate(term) {
    let terms = {
        '1': '一',
        '2': '二',
        '3': '三'
        '4': '四'   // 添加一个新的名词翻译
    }
    return terms[term];
}
```
## 如何理解模块化
模块化顾名思义就是让整个系统由一个个模块组成，在通过模块间的连接而去完成整个系统。模块化的过程就是拆分、归纳、总结的过程：  
* 拆分  
将整个系统按功能,格式,加载顺序,继承关系分割为一个一个单独的部分.  

* 归纳  
将功能或特征相似的部分组合在一起,组成一个资源块.   

* 总结  
将每个资源块按找需求,功能场景以及目录约束放到固定的地方以供调用.  

将编程变成模块化之后，能够增强系统框架的设计，让开发者便于维护，同时也让逻辑相同的部分可复用，对于大型开发而言，模块化是必不可少，且占据着极为重要的地位。