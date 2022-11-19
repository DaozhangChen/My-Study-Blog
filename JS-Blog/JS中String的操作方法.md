本文内容参考《JavaScript高级程序设计（第四版）》
# JavaScript字符
在JS中字符是由16位码元（code unit）组成，每一个码元就是一个字符，也就两个码元形成的字符。可以通过**length**属性来查看某一个字符串中16位码元的个数。
## 操作码元的方法
操作码元的方法主要有charAt、charCodeAt、fromCharCode、codePointAt、fromCodePoint  
* chartAt 该方法接受一个整数参数，返回字符串中该参数位置的字符串
* chartCodeAt 该方法接受一个整数参数，返回字符串中该参数位置字符串的码元值
* fromCharCode 该方法接受多个UTF-16码元的参数，返回所有参数结合起来的字符串
* codePointAt 与charCodeAt类似，接受一个整数参数，返回字符串中该位置的码点，一般用于处理charCodeAt无法正确解析的字符串
* fromCodePoint 与fromChartCodeAt类似，用于处理fromChartCodeAt无法解决的内容

```javascript
let a = 'abcde'

a.charAt(2)                     //'c'
a.charCodeAt(2)                 //99
String.fromCharCode(97,98,99)   //'abc'


let b = 'ab😊'
//codePointAt可以一次解析双码元字符
b.charCodeAt(2)   //55357
b.charCodeAt(3)   //56842
b.codePointAt(2)  //128522

String.fromCharCode(97,98,55357,56842)  //'ab😊'
String.fromCodePoint(97,98,128522)      //'ab😊'
```
# 字符串操作方法
JS中对字符串的增删通常使用以下方法concat、slice、substr、substring
## concat方法
使用concat可以使多个字符串合成一个新的字符串，concat接受多个字符串参数。
```javascript
let a = 'Hello'
let b = a.concat('world','!')
console.log(b)  //'Hello world !'
console.log(a)  //'Hello'
```
## slice、substr、substring
从字符串中提取新的字符可以用这些方法，这些方法都接受一个或两个参数，第一个参数是操作开始的位置，第二个参数是结束的位置。  
对于slice和substring而言，第二个参数是提取结束的位置，对于substr而言，第二个参数表示返回字符串的长度。若省略第二个参数，则默认第二个参数指向最后一个字符的位置
```javascript
let a = 'hello world'
a.slice(3)      //'lo world'
a.substring(3)  //'lo world'
a.substr(3)     //'lo world'
a.slice(3,7)    //'lo w'
a.substring(3,7)//'lo w'
a.substr(3,7)   //'lo worl'
```
如果参数出现了负值，则结果会有些许不同  
slice会将所有的负值都当做length长度加上负值进行运算，得出的值就按正值来算  
substr会将第一个参数负值的值进行运算，也是length加上负值，第二个参数的负值直接设为0  
substring会将所有的负值参数都转化为0
```javascript
let a = 'hello world'

a.slice(-3)      //'rld'
a.substring(-3)  //'hello world'
a.substr(-3)     //'rld'
a.slice(3,-4)    //'lo w'
a.substring(3,-4)//'hel'
a.substr(3,-4)   //''
```
# 字符串的位置方法
## indexOf、lastIndexOf
与数组相同，indexOf接受两个参数，第一个参数是需要查找的字符串，第二个参数是开始查找的位置，两个方法查找的开始方向相反，返回寻找到符合的第一个字符的位置，若没找到则返回-1  
```javascript
let a = 'hello world'
a.indexOf('o',6)      //7
a.lastIndexOf('0',6)  //4
```
# 字符串的包含方法
## startWith、endsWith、includes
这些方法都接受两个参数，第一个参数是要查找的字符串，第二个参数是可选的开始查找的位置。  
startWith查找从查找位置开始是否有匹配的字符串，endsWith则相反，includes则是查找整个字符串是否包含需要的字符串
```javascript
let a = 'foobarbaz'
a.startWith('foo')      //true
a.startWith('foo',1)    //false
a.includes('bar')       //true
a.includes('bar',4)     //false
a.endsWith('bar')       //true
a.endsWith('bar',6)     //false
```
# 字符串的填充方法
## padStart、padEnd
padStart就是在字符串前填充，padEnd就是在字符串后填充。  
都接受两个参数，一个参数是预期的字符串长度，第二个是可选的填充字符，默认是空格，若填充字符长度大于需要被填充的长度，则会被省略一部分进行填充,若第一个参数大小小于字符串大小，则不会对字符串进行改变。
```javascript
let a = 'foo'
a.padStart(8,'bar')   //'barbafoo'
a.padStart(2)         //'foo'
a.padEnd(8,'bar')     //'foobarba'
a.padEnd(2)           //'foo'
```
# 字符串大小写转换方法
有四个方法，toLowerCase、toLocalLowerCase、toUpperCase、toLocalUpperCase。
toLowerCase会把所有的字符串变为小写的字符串，toUpperCase则是会把所有字符串变为大写。  
带有Local的api用于转化一些国家的特定字符，通常我们使用不带Local版本的即可。
```javascript
let a = 'hello'
let b = 'HELLO'

a.toUpperCase()  //'HELLO'
b.toLowerCase()  //'hello'
```
# 字符串模式匹配方法
## match、search、replace
match接受一个正则表达式，会返回一个数组，数组的第一项是匹配的字符串，第二个参数是匹配字符串的位置，如果没有匹配到则会返回一个null  
search接受一个正则表达式会返回第一个匹配到字符的索引，如果没有匹配则会返回一个-1  
replace可以是字符串中被匹配到的内容进行替换，接受两个参数，第一个参数是正则表达式，第二个参数是需要替换成的字符串
```javascript
let text='cat bat sat fat'
let pattern=/.at/
let pattern2=/at/

let a = text.match(pattern)
a[0]                         //cat
a.index                      //0
text.search(pattern2)        //1
text.replace(pattern,'hhh')  //'hhh bat sat fat'
```
## split
split用于分割匹配的字符串变为一个数组，接受两个参数，第一个参数是匹配分割位置的字符，可以是字符串，也可以是正则表达式，第二个参数是可选的分割成的数组的大小，不填则不会对长度做限制。
```javascript
let a = 'cat,bat,sat,fat'
a.split(,)     //['cat','bat','sat','fat']
a.split(,,2)   //['cat','bat']
```
