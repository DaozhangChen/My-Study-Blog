本文参考《JavaScript高级程序设计（第四版）》中的内容。
# 创建数组
在JS中创建数组有四种方式，两种为传统方式，两种为ES6新增方式。
## 创建数组的传统方式
### new Array()
```javascript
new Array(5)  //创建一个长度为5的空数组
new Array('foo','bar')  //['foo','bar']
```
使用Array()创建数组时，如果输入的参数只有一个，且为数字，就会创建一个与参数数值相等长度并存在数组空位的数组。
### 数组字面量
```javascript
let colors=['red','blue']  //['red','blue']
let name=[]  //[]空数组，length为0
let n=[1,2,]  //[1,2]，length为2
```
## ES6新增数组创建方式
分别是Array.from()和Array.of()。from()用于将类数组结构转化为数组实例，而of()用于将一组参数转换为数组实例。  
直接说两者的区别：  
```javascript
如果传递一个字符串，两者的输出会有区别：
Array.from('Matt')  //['M','a','t','t']
Array.of('Matt')    //['Matt']

如果传递的是一个对象，from会转化为只有值存在的数组，of会转化成一个只有一项对象的数组
const a = {
    0:1,
    1:2,
    2:3,
    length:3  //length一定要有，不然from会转化成一个空数组
}
Array.from(a) //[1,2,3]
Array.of(a)   //[{0:1,1:2,2:3,length:3}]

如果遇到单独的undefined与null，from无法转换为数组
Array.from(undefined)  //语法错误
Array.of(undefined)   //[undefined]
```
Array.from一共接受三个参数，第一个是打算变化的数组，第二个是用于操作数组的函数，第三个是用于指定映射函数中this的值
```javascript
const a1=[1,2,3,4]
const a2=Array.from(a1 , x => x**2)
const a3=Array.from(a1 , function(x){return x ** this.exponent},{exponent:2})

console.log(a2)  //[1,4,9,16]
console.log(a3)  //[1,4,9,16]
```