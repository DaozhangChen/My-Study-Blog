# JS中的this

## 为什么要使用this
关于什么是this，首先要知道使用this会解决什么问题。  
我们先创建一个对象：  
```javascript
let obj={
    name:'小白',
    dogName:function(){
        console.log(obj.name)  //小白
    }
}
```
之后我们调用obj中的dogName方法，就可以使用这个函数，打印出obj.name。但是如果声明了一个函数，要在别的对象中引用该函数的方法，就会发现问题所在，因为会发现这个dogName中的name始终无法指定，除非单独写入对象中，再依次修改。
```javascript
let dogName=function(){
    console.log(`你好，我叫`+   name?  )  //这里的name到底是谁的name
}
let dog2={
    name:'小花'
    dogName:dogName
}
```
如果创建一个class类，那就更能够理解这一点问题：
```javascript
class Dog{
    constructor(name){
        this.name=name
        //这里的name是new强行指定的
    }
    dogName(){
        console.log(???)
    }
}
```
这里就更写不出来了，因为你不知道未来创建的对象到底叫什么名字，因此不可能采用对象的引用，那么如何拿到name呢？  
这时候就可以用this了(当然也可以用形参来代替this，但是这样引用的时候就会变得很丑)  
```javascript
class Dog{
    constructor(name){
        this.name=name
        //这里的name是new强行指定的
    }
    dogName(){
        console.log('这条狗的名字叫'+this.name)
    }
}
```
这样当你在用Dog类来实例化一个新对象时，使用这个方法，该方法就会自动的将对象的参数以this的方式传入函数里。这样一来，就可以不用知道对象的名字，也可以在类中添加一个新方法了。
## this到底指向谁？
关于this的指向，每个人都有每个人的说法，我自己的理解是：谁调用了这个方法，那么this就指向谁。
来两道面试题：
```javascript
var name='小白';
function special(){
    console.log('姓名:'+this.name);
}
var girl={
    name:'小红',
    detail:function(){
        console.log('姓名:'+this.name);
    },
    woman:{
        name:'小黄',
        detail:function(){
            console.log('姓名:'+this.name);
        },
    }
    special:special
}

girl.detail();  //小红
girl.woman.detail();  //小黄
girl.special();  //小红
```
解析：  
girl.detail()为什么打印出小红？  
因为此时调用的是girl中的detail方法，那么对于这个方法来说，它是girl中的方法，那么此时的this也是指向的girl中的name。

girl.woman.detail()为什么打印出小黄？  
因为此时调用的是girl中woman对象中的一个方法detail,那么对于这个方法来说，他是处于woman中的一部分，那么它的this就是指向woman中的name。  

girl.special()为什么打印出小红？  
因为此时调用的是girl中的special方法，girl.special采用了之前声明的special函数，文中可以等价于将special函数的内容全部搬进girl对象中，那么此时调用special，就是在girl对象中调用special（这句话好像重复了。。），那么这个方法中的this就自然是指向girl中的name了。

还有下一道题：
```javascript
var name='小红'
function a(){
    var name = '小白'
    console.log(this.name)
}
function d(i){
    return i()
}
var b = {
    name:'小黄',
    detail:function(){
        console.log(this.name)
    },
    bibi:function(){
        return function(){
            console.log(this.name)
        }
    }
}
var c = b.detail
b.a = a
var e = b.bibi()

a();  //小红
c();  //小红
b.a(); //小黄
d(b.detail); //小红
e(); //小红
```
解析：  
a()为什么打印出小红？  
因为当我们调用a函数时，此时a函数是一个全局函数，也就是说它的this也是指向全局的，那么在全局中的name，只有在全局作用域定义的`var name='小红'`了。  

c()为什么打印出小红？  
因为此时c赋值了b中的detail方法，那么此时的c就是b中的detail方法，但只是内容相同，使用上没有任何关系，那么这时候c就与a一样，是一个全局函数，那么this也是指向全局的name=小红了。(*赋值时b.detail没有调用，只是将方法给了c,如果是`c=b.detail()`,那么此时的c就是b.detail调用后的值--undefined*)  

b.a()为什么打印出小黄？  
因为此时在b中增加了一个方法a，那么调用b中的方法a，a的this就指向了b中的name，也就是小黄了。  

d(b.detail)为什么打印出小红？
首先要明白d函数是什么意思，d函数的意思是，接受一个参数，然后返回这个参数的调用，那么我们就可以把b.detail这个函数单做参数传入d中，然后就会调用b.detail这个参数了，那么调用的这个参数与c一样，只是方法，调用时与b.detail没有任何关系，那么d函数是一个全局的函数，那么this也就会指向全局的name小红了。  

e()为什么打印出小红？  
此时e被赋值了b.bibi(),此时b.bibi被调用了，那么调用后的bibi会返回一新的函数，那么此时的e就是等于这个新的函数，那么调用e就是调用这个新函数，由于e是全局的函数，那么this就是指向全局的name了。  

好好思考这两个面试题，就可以大概想明白this的指向。上题中有个很简单的规律，那就是看调用时函数点前面的是什么，那么this就是什么，什么意思呢？比如a()的this就是window，也就是全局;b.a()的this就是b，看函数最后被调用前点前面的对象是什么，那么this就是什么。
## 绑定this指向
JS中提供了三种能够绑定this的指向，分别是call、apply和bind。  
他们的区别在于call和apply，只要使用，函数就会被调用，bind不会直接调用，而是会生成一个新函数，需要去主动调用这个新函数。  
call和apply的区别在于，call和bind传入的参数是多个单独的变量，而apply传入的参数是一个数组。  
他们的共同点还有一个，就是传入参数的第一个值就是需要指定给函数的this值。  
语法如下：  
```javascript
function.call(thisArg, arg1, arg2, ...)
function.apply(thisArg, argsArray)
function.bind(thisArg[, arg1[, arg2[, ...]]])
```
