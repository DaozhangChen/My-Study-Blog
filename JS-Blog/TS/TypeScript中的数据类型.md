# 什么是TypeScript？
TypeScript在语言特性方面和JavaScript基本一样，但是在用法上会有些许不同，可以这么说TypeScript是JavaScript的超集，也就是说，TS包含了JS。  
TS与JS在使用中最大的不同点可能就是需要标注数据的类型了，下面总结了在TS中所有的数据类型以及它的用法。
# TS的基础类型
在TS中，主要有这几种基础类型：boolean、number、string、array、tuple、enum、any、void、never，下面依次来说明这些类型的不同。
## boolean（布尔类型）
布尔类型与JS中的布尔类型一样，只有两个取值，true或者false
```typescript
let a:boolean=true
//a = 123  //报错
a = false  //正确
```
## number（数字类型）
数字类型也和JS中一样，TS中所有的数字都是浮点数，TS不仅支持十进制和十六进制字面量，还支持二进制和八进制字面量
```typescript
let decLiteral: number = 6;  //十进制
let hexLiteral: number = 0xf00d;  //十六进制
let binaryLiteral: number = 0b1010;  //二进制
let octalLiteral: number = 0o744;  //八进制
```
## string（字符串类型）
和JS中一样，可以使用单引号（''）和双引号（""）表示字符串，也可以使用反引号（``），并且以`${ expr }`这种方式嵌入表达式
```typescript
let name2: string = 'Gene';
let name1: string = "frank"
let age: number = 37;
let sentence: string = `Hello, my name is ${ name2 }.
I'll be ${ age + 1 } years old next month.`;
```
## array（数组类型）
数组在TS中有两种声明方法，一种是在元素类型后面接上[]，表示由此类型元素组成的一个数组，第二种是使用数组泛型`Array<元素类型>`。
```typescript
let list : number[] = [1,2,3];
let list : Array<number> = [1,2,3];
```
## tuple（元组类型）
元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同。如果赋值的类型和数量与声明时的不一致，就会报错。
```typescript
let x: [string, number];
x = ['hello', 10]; // OK
x = [10, 'hello']; // Error
```
## enum（枚举类型）
使用枚举类型可以为一组数据赋予友好的名字，默认情况下，花括号中的数据会按数字0开始排列
```typescript
enum Dir {东,南,西,北}
let d:Dir=Dir.东
console.log(d)       //0
console.log(Dir[0])  //东
```
当然你可以手动给数据赋值
```typescript
enum Dir {东=2,南=1,西='hello',北=5}
console.log(Dir[0])  //undefined
console.log(Dir[2])  //东
console.log(Dir.西)  //'hello'
```
## any（任意类型）
可以指定任意类型，一般是用于为那些在编程阶段还不清楚类型的变量指定一个类型
```typescript
let num:any = 123;
num = 'str';   //OK
num = true;    //OK
```
## void（空值类型）
某种程度上来说void类型与any类型相反，当一个函数没有返回值，那么这个返回值类型就是void
```typescript
function a() : void {
    console.log('hello')
}
```
## null和undefined
这两个类型的值就是类型名称，默认情况下null和undefined是所有类型的子类型，也就是说可以把null和undefined赋值给number类型的变量
```typescript
let u: undefined = undefined;
let n: null = null;
```
## Never
Never类型表示的是那些永不存在的类型，这个常常会出现在选择判断中
```typescript
let a:number|string=123

if(typeof a === 'number'){
    a=321
}else if(typeof a === 'string'){
    a='321'
}else{
    a  //此时a类型推断为never
}
```
# TS中的高级类型
## 交叉类型
让多种类型使用`&`符号连接起来，这样的一个新类型，就同时拥有了其他多种类型的特性，比如我们需要一个既有Person类型，又有Animal类型，就可以这样
```typescript
type Person = {
    name:string,
    age:number,
    gender:'Male'|'Female'
}
type Animal = {
    variety:string,
    footNumber:number
}
type Cross = Person & Animal
let a:Cross={
    name:'fang',
    age:18,
    gender:'Male',
    variety:'human',
    footNumber:2,
}
```
但如果Person类型和Animal类型有一个**相同key的类型是冲突**的，那么这个类型就不存在了，表现为Never
## 联合类型
联合类型在语义上表现为`或`，这个类型在之前就已经见过了，比如一个值，可以是number类型也可以是string类型，那么就可以用`|`运算符联合两个类型
```typescript
let a:number|string
a=1   //ok
a='1' //ok
```
如果联合类型是两个对象联合，那么就会要求这个新的联合对象，一定要包含其中一个对象中完整的内容，另一个则不作要求，此时如果对象中的属性有冲突，也不会报错。
```typescript
type Airplane = {
    fly:()=>void,
    length:string
}
type Car = {
    drive:()=>void,
    length:number
}
type Unit = Airplane | Car
let a:Unit={
    fly:()=>undefined,
    length:'100'
    //length:100  //如果为number就会报错，因为number不是Airplane的length属性
}
