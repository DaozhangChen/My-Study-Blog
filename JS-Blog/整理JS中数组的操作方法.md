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
## ES6新增数组创建方式(Array.from/Array.of)
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
# pop、push、shift、unshift方法
这四个方法用于操作数组的头尾的单个数据，用法如下：
```javascript
let a = [0,1,2,3,4,5]
分别使用以下操作：

a.pop()        //return 5 , a=[0,1,2,3,4]
a.push(5)      //return 7 , a=[0,1,2,3,4,5,5]
a.shift()      //return 0 , a=[1,2,3,4,5]
a.unshift(0)   //return 7 , a=[0,0,1,2,3,4,5]
```
pop和shift方法会分别从数组尾部和头部删除一个数，并返回被删除的数。
push和unshift则相反，是往数组内添加数，返回值是数组的最新长度(即a.length)。
# 数组的迭代方法与归并方法
## 迭代方法(every/filter/map/forEach/some)
迭代方法是指对数据中的每个元素依次遍历，并执行回调函数，通过回调函数返回新的结果的方法。   
其中每个方法接受两个参数，一个参数是函数中需要执行的回调函数，第二个是可选的用于规定函数中this指向的值。
内部的回调函数接受三个值，分别为数组元素，元素索引，数组本身。
```
every()：对每一项都执行回调函数，如果每一项函数都返回true，则这个方法返回true，否则为false
filter()：对数组的每一项执行函数，函数返回true的项会组成数组后返回
forEach()：对每一项执行函数，没有返回值
map()：对每一项执行函数，返回由每次函数调用的结果构成的数组
some()：对每一项都运行传入的函数，如果有一项函数返回true，那么这个方法返回true，反之为false
```
**以上方法都不会改变原数组的值**
```javascript
示例：
let a = [0,1,2,3,4,5]
a.every(x=>x>=0)  //return true
a.filter(x=>x>2)  //return [3,4,5]
a.forEach(x=>x)   //return undefined
a.map(x=>x*2)     //return [0,2,4,6,8,10]
a.some(x=>x>3)    //return true
```
## 归并方法(reduce/reduceRight)
归并方法也会遍历每个数组元素，并在此基础上构建一个最终返回值  
归并方法有两个，reduce和reduceRight，两个方法都接受2个参数，分别是用于执行的回调函数，和一个可选的设定初始值；  
回调函数接受4个参数的分别是上一个归并值，当前项，当前项的索引，和数组本身，这个方法返回的任何值都会作为下一次调用函数的第一个参数，如果没有设定初始值，那么就默认以第一项的值为初始值，并会从数组的第二项开始遍历。  
两个方法的区别在于，reduce遍历数组是从第一项开始到最后一项遍历，而reduceRight则是相反。  
```javascript
let a = [0,1,2,3]
a.reduce(
    (prev,cur)=> {
        console.log('prev的值为'+prev)
        console.log('cur的值为'+cur)
        return prev+cur
    },1
)
如果传递初始值，则会从第一项开始遍历
//prev的值为1
//cur的值为0
//prev的值为1
//cur的值为1
//prev的值为2
//cur的值为2
//prev的值为4
//cur的值为3
//return 7

如果不传递则会从第二项开始遍历
a.reduce(
    (prev,cur)=> {
        console.log('prev的值为'+prev)
        console.log('cur的值为'+cur)
        return prev+cur
    }
)
//prev的值为0
//cur的值为1
//...
//return 6

reduceRight的遍历方向相反
a.reduceRight(
    (prev,cur)=> {
        console.log('prev的值为'+prev)
        console.log('cur的值为'+cur)
        return prev+cur
    },1
)
//prev的值为1
//cur的值为3
//prev的值为4
//cur的值为2
//...
//return 7
```
# 搜索和位置方法
搜索和位置方法有两种，一种为严格相等，一种为断言函数
## 严格相等方法(indexOf/lastIndexOf/includes)
有三个方法来进行严格相等，分别是indexOf、lastIndexOf、includes，每个方法都接受2个参数，一个是要查找的元素，另一个是可选的开始查找的位置。  
indexOf查找方向为从第一项查找到最后一项，lastIndexOf查找方向是从最后一项查找到第一项，二者返回值相同，都是查找到的元素索引，如果没有找到该元素则返回-1，includes则是返回布尔值。
```javascript
let a = [1,2,3,4,5,4,3,2,1]

a.indexOf(4)      //3
a.lastIndexOf(4)  //5
a.includes(4)     //true

a.indexOf(4,4)      //5
a.lastIndexOf(4,4)  //3
a.includes(4,7)     //false
```
在使用这种方法时，对比的每一项需要是全等（===），特别是对比数组中的对象时。
```javascript
let a={b:1}
let i=[{b:1}]
let I=[a]

i.indexOf(a)   //-1
I.indexOf(a)   //0
```
## 断言函数(find/findIndex)
可以通过自定的函数来描述需要在数组中寻找的元素，有2种方法find、findIndex，find返回符合函数的第一个元素，findIndex返回符合函数的第一个元素的下标，找到一个符合条件的元素后就不会再进行遍历。  
该方法接受一个断言函数，函数接受三个参数，元素本身、元素索引、数组本身。  
如果find返回的是个对象，则对这个返回对象中的值进行修改，也会同步到原数组中。
```javascript
let people=[
    {name:'matt',age:27},
    {name:'kun',age:22}
    ]

people.find(x=>x.age>20)  //{name:'matt',age:27}
people.findIndex(x=>x.age>20)  //0  
```
# 复制与填充方法(fill/copyWithin)
填充方法：fill，接受三个参数：一个用于填充的元素，一个可选的起始填充位置，一个可选的结束填充位置  
复制方法：copyWithin，接受三个参数，有不同的情况
**这两个方法均会改变原数组**
```javascript
const a = [0,0,0,0,0]
a.fill(5)      //[5,5,5,5,5]
a.fill(5,1,3)  //[0,5,5,0,0]

let b = [0,1,2,3,4,5,6,7,8,9]
//从0开始复制数组，插入到索引5
b.copyWithin(5)     //[0,1,2,3,4,0,1,2,3,4]
//从索引5开始复制，插入到索引0
b.copyWithin(0,5)   //[5,6,7,8,9,5,6,7,8,9]
//从索引0到3开始复制，插入到索引4
b.copyWithin(4,0,3) //[0,1,2,3,0,1,2,7,8,9]
```
若复制或填充的数量超过了数组的长度，则超出数组长度的部分会被省略。
# 数组转换为字符串方法(toString/toLocaleString/join)
常用有3种toString、toLocaleString、join，二者都是把数组变为用逗号分隔的一个字符串，并且会打平整个数组。
```javascript
let a = [0,[1,2],[3,4,[5,6]]]

a.toString()        //'0,1,2,3,4,5,6'
a.toLocaleString()  //'0,1,2,3,4,5,6'
//两者在极少情况才会发生不同，可以不用在意
```
join方法接受一个参数，会把变成字符串的默认逗号分隔，变为自己想要的分隔符。但是这个分隔符只能对第一层的数组有作用，内部的嵌套数组还是使用默认的逗号分隔。
```javascript
let a = [0,[1,2],[3,4,[5,6]]]

//不填参数效果与toString相同
a.join()     //'0,1,2,3,4,5,6'
a.join('')   //'01,23,4,5,6'
a.join(|)    //'0|1,2|3,4,5,6'
```
# 数组的排序方法(reverse/sort)
有2中，reverse和sort，reverse方法会将原数组直接进行反转，返回一个前后颠倒的原数组。sort则更为灵活，接受一个用于排序的回调函数，接受两个参数，用于判断哪个值排在前面，如果第一个参数排在第二个参数前面，就返回负值，如果两个参数相等，就返回0，如果第一个参数应该排在第二个参数后面，就返回正值。
```javascript
let a = [1,2,3,4,5,6]

a.reverse() //[6,5,4,3,2,1]

function compare(value1,value2){
    if(value1 < value2){
        return 1
    }else if(value1 > value2){
        return -1
    }else{
        return 0
    }
}
a.sort(compare) //[6,5,4,3,2,1]
```
# 数组的其他操作方法
## 结合数组及类数组(concat)
使用concat来使一个或多个数组结合为一个大数组。该方法会默认打平**第一层**的数组，但是可以解除这个默认值。遇到类数组对象需要打平是，则需要设置强制打平参数
```javascript
let a = [1,2,3]
let b = [5,6]
let c ={
   [Symbol.isConcatSpreadable]:true,  //强制打平
   0:7,
   1:8,
   length:2
}

b[Symbol.isConcatSpreadable]=true //取消默认打平
a.concat(a) //[1,2,3,1,2,3]
a.concat(b) //[1,2,3,[5,6]]
a.concat(c) //[1,2,3,7,8]
```

## 创建包含原数组的新数组(slice)
slice方法用于创建一个包含原数组中一个或多个元素的新数组，该方法接受一个或两个参数，只有一个参数则会返回该索引到数组末的所有元素，如果有两个参数，则会返回开始索引到结束索引的所有元素。
```javascript
let a = [0,1,2,3,4]
a.slice(2)   //[2,3,4]
a.slice(0,3) //[0,1,2]
```
## 删除插入替换的一体化方法(splice)
或许最强大的数组方法就属于splice了   
该方法接受三个参数，第一个是开始操作的位置，第二个是需要删除数组的量，第三个是在该位置新增的元素,该方法返回被删除的元素数组，**所有操作均会影响原数组**。
```javascript
let color = ['red','green','blue']

//从0开始删除一个元素
color.splice(0,1)  //['green','blue'] return ['red']

//在1位置插入一个元素
color.splice(1,0,'black')  //['red','black','green','blue'] return []

//插入一个值，删除一个值
color.splice(1,1,'orange') //['red','orange','blue'] return ['green']
```