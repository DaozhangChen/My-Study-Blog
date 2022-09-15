# 跨域与CORS、JSONP
## 同源策略
要了解什么是跨域，首先要了解浏览器的同源策略。浏览器为了防止不同源之间进行非法的数据请求，而规定的策略，也就是说，浏览器限制了不同源之间的数据请求，使两个不同源网站不能进行非法的沟通。   
我们首先要了解源的定义：  
任何一个网页打开window.origin或location.origin可以得到当前源   
源=协议+域名+端口号   
如果两个url的   
* 协议
* 域名
* 端口号
* 完全一致，那么这两个url就是同源的  
比如  
* https://qq.com、和http://www.baidu.com不同源
* https://baidu.com、https://www.baidu.com 不同源
* 完全一致才算是同源   

为什么我们需要同源策略呢？   
原因有以下几点：  
* 对于一个文件的请求，发的请求几乎没有区别，只有referer有区别
* 如果后台开发者没有检车referer，那么就完全没有区别
* 为了防止有些不靠谱的后端，浏览器就自己弄了一个最开始的保护  


同源策略带来了许多安全方面的好处，可以很好的保护用户隐私，但实际上保护用户隐私不止这一环，还有许许多多的要求，但这里是整个安全链条最开始的部分之一。  
## 什么是跨域？CORS？JSONP？
当两个不同源之间想要进行数据访问，通过一些技术手段实现，这个过程就是叫跨域。   
目前的跨域方式有许多种，常见也是最经典的方式就是CORS和JSONP两种。   
### CORS
CORS （Cross-Origin Resource Sharing，跨域资源共享）是一个系统，它由一系列传输的HTTP 头组成，这些 HTTP 头决定浏览器是否阻止前端 JavaScript 代码获取跨域请求的响应。   
那么如何使用CORS进行跨域呢？
只需要两步，假如想要获取数据的网站叫做http://cors.com:8888,想要获取到http://shuju.com:9999/code.json这个路径的数据：   
第一步，先要到cors.com这个网站中修改XMLHttpRequest对象上修改它的open路径，路径为http://shuju.com:9999/code.json，一定要一模一样，否则不能请求成功。   
第二歩，到shuju.com这个网站中的服务器文件中修改响应头，增加一个setHeader，全部内容为：  
```javascript
response.setHeader('Access-Control-Allow-Origin','http://cors.com:8888')
```
路径也要和请求网站完全一致，包括端口号，只要写成这样就可以成功用CORS跨域了。   

如果有两个网站是不是也要这么写呢？   
多个网站一般是读取网站的referer，也就是`request.headers['referer']`然后再判断是否来自这个网站即可。  
至此，CORS讲完了，CORS就是这样简单。   
但是有一个很大的问题，那就是IE不支持。。。    
那咋办？   
就得用JSONP了。
### JSONP
JSONP(JSON with Padding) 是 json 的一种"使用模式"，可以让网页从别的域名（网站）那获取资料，即跨域读取数据。   
核心思想：   
由于网站间的JS是可以互相引用的，通过替换符，将JS中的内容替换成JSON文件中的内容，那么就可以让包含数据的JS文件被引用就可以实现了。  
实现步骤：  
以请求网站为http://jsonp.com:8888,数据网站同上为例，在shuju.com网站中同时创建两个文件，一个是需要被引用的JS文件，一个是所包含数据的JSON文件。   
之后需要做三件事：  
1. 在JS文件中插入一个需要被替换的符号，比如`{{data}}`
2. 在服务器文件中写入请求JS文件的路由(*`if path === '一个url'`*)，并同时读取文件内容

```javascript
const string=fs.readFileSync('JS文件路径').toString()
const data=fs.readFileSync('JSON文件路径').toString()
```
3. 最后再将替换符换成JSON文件的内容  
```javascript
const string2=string.replace('{{data}}',data)
response.write(string2)
```
在服务端的操作结束了，下面开始在jsonp.com网站上进行操作。   
在这个网站上只需要做一步，就是将一个script标签用dom操作插入到自己的html文件里。   
```javascript
const script = document.createElement('script')
script.src = 'http://shuju.com:9999/js文件路径'
document.body.appendChild(script)
```
这样，就完成了JSONP的跨域。   

**但是这样不是所有网站都能用了吗？**  
是的，但是我们可以做个referer检查呀。
### referer与functionName
我们可以通过检查网站的referer来避免这一点，在数据的服务器文件中，该文件的路由下写一个判断条件：   
```javascript
if(request.headers['referer'].indexOf('http://jsonp.com:8888')===0){
//这句话就是判断是否为这个路径开头的网站
//判断成功之后才进行数据的交流
response.end()
}else{
    response.statusCode=404  //如果不是来自这个网站就提示404
    response.end()
}
```   
我们在替换的js文件中采用了`windows.xxx={{data}}`的方式进行数据替换并可以在引用文件的控制台中用window.xxx得到数据。   
但是如果我们的数据有很多呢，这个xxx能不能替换成用不重复的符号来代替？   
可以用随机数来代替，并且这个随机数可以通过查询参数传递给后台，直接上代码：   
```javascript
const random = Math.random()

const script = document.createElement('script')
script.src = `http://shuju.com:9999/js文件路径?functionName=${random}`
document.body.appendChild(script)
```
然后可以在服务器文件中使用`query.functionName`,再次进行同样的替换
```javascript
//此时服务器文件中的window.xxx={{data}}已经修改为
//window['{{xxx}}']({{data}})
const string2=string.replace('{{data}}',data).replace('{{xxx}}',query.functionName)
```
这样就可以永远不跟任何变量重复了。    

最后再优化一点，由于jsonp使用script引用的方式来获取数据，如果数据量多，也就是script标签会越来越多，这样会使页面 变得臃肿，可以在文件请求完成后再删除，这样既不会使页面臃肿，又可以获取到数据（是的，删除引用后也可以获取数据）   
```javascript
script.src = `http://shuju.com:9999/js文件路径?functionName=${random}`
script.onload=()=>{
    script.remove()
}
document.body.appendChild(script)
```