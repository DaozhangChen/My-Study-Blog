# jQuery初学笔记
jQuery是目前为止最为长寿也最多人使用的JavaScript函数库，到目前为止，仍有近70%的网站使用这个库，最近也是学习了这个库的使用，本文的主要内容有：  
1. jQuery如何获取元素
2. jQuery 的链式操作是怎样的
3. jQuery 如何创建元素
4. jQuery 如何移动元素
5. jQuery 如何修改元素的属性
## jQuery如何获取元素
jQuery可以通过选择器的不同，获取到不同选择器上的元素，jQuery()可以简写成$()比如：
* $(document) //选择整个文档对象
* $('#test') //选择id为test的元素
* $('div.xxx') //选择class为xxx的div元素
* $('input[name=first]') //选择name为first的input元素  
意思是，通过调用jQuery()可以为后续进行的操作定位，jQuery可以精确的定位到你想进行操作的任何位置。
## jQuery 的链式操作是怎样的
jQuery中非常具有特色的一点是可以进行链式操作，比如说，我需要修改div中的第三个h3元素的内容为Hello，就可以写成：  
```javascript
$('div').find('h3').eq(2).html('Hello');
```
分解开来就是：
```javascript
$('div') //找到文档中的div元素
   .find('h3') //找到div中的h3元素
   .eq(2) //找到第三个h3元素
   .html('Hello') //修改内容为'Hello'
```
贴心的jQuery还准备了end()方法，使结果集可以推后一部
```javascript
$('div')
   .find('h3')
   .eq(2) 
   .html('Hello')
   .end() //返回至选择第几个h3的那一步
   .eq(0) //选择第一个h3元素
   .html('World') //修改内容为World
```
## jQuery 如何创建元素
jQuery中创建元素可以直接用$()来进行创建
```javascript
$( "<p id='test'>My <em>new</em> text</p>" )
//这样就可以创建一个id为test的p元素，并且里面还存在着其他的html元素
//创建完之后，我们可以用appendTo()来将创建好的元素插入到指定位置，比如：
$( "<p id='test'>My <em>new</em> text</p>" )appendTo("body")
//就可以将这个创建好的元素插入到body中了
```
## jQuery 如何移动元素
在jQuery中，提供了两种方法来移动元素：  
```javascript
$('div').insertAfter($('p')); //把div元素移动到p元素后面


$('p').after($('div')); //把p元素移动到div元素后面
```
两种方法看起来相同，但是是有区别的，前者返回的是div元素，后者返回的是p元素，可以自己根据需要，选择使用哪种方法。
## jQuery 如何修改元素的属性
可以用.attr()的方法来修改  
一共有四种语法：
```javascript
$(selector).attr(attribute)       //获取匹配的元素集合中的第一个元素的属性的值
$(selector).attr(attribute,value)    //设置被选元素的属性和值
$(selector).attr(attribute,function(index, attr))   //用函数返回值设置被选元素的属性和值
$(selector).attr({attribute:value,attribute:value ...})    //同时修改多个属性的属性值
```
其中，attribute就是需要设置修改的属性名，value是修改的属性值。
## 后话
jQuery还有很多其他功能，等日后整明白了再写一篇更加详细的说明。