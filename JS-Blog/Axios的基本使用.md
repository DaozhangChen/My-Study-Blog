Axios是一个基于Promise的网络请求库，作用于node.js和浏览器中。它能做到以下几个特点：
* 发送请求
* 拦截请求和响应数据
* 取消请求
* 自动转换JSON数据  
下面是他的具体用法：  
# 发送请求
在Axios中发送请求有两种方法，第一种是通过具体请求的方法发送，第二种是使用传递配置对象发送
## 通过具体请求方法发送
比如说想要发送一个get请求，就可以这样
```js
//引入axios
const axios = require('axios');

// 向给定ID的用户发起请求
axios.get('/user?ID=12345')

//当然这里也可以写成这种方式
axios.get('/user', {
    params: {
      ID: 12345
    }
  })
```
以上两种方式是等价的，都是发送一个get请求，并且有一个查询参数ID为12345  
如果想要发送其他方法的请求也是同理  
```js
axios.post()
axios.delete()
axios.patch()
...
```
## 使用传递配置对象的方式发送
这里发送一个post请求
```js
axios({
  method: 'post',
  url: '/user/12345'
});

//这样就会向'/user/12345'发送一个post请求
```
以上两种方式发送完请求后，会返回一个promise对象，我们就可以通过then、catch等Promise方法来捕获数据了。  
需要注意的是，所有的方法中，url是必须的，如果在配置中没有写具体的请求方法，axios默认会以get方法发送请求。
# 创建实例
可以通过`axios.create()`来创建一个实例，这个实例会将你自己的配置覆盖axios的默认配置，并且每次使用这个实例发送请求时，都会使用你自己的配置。  
```js
const instance = axios.create({
  baseURL: 'https://some-domain.com/api/',
  timeout: 1000,
  headers: {'X-Custom-Header': 'foobar'}
});
```
这个`instance`实例也可以使用上面的两种方法发送请求
# 请求配置
下面是常用的请求配置
```js
{
  // `url` 是用于请求的服务器 URL
  url: '/user',

  // `method` 是创建请求时使用的方法
  method: 'get', // 默认值

  // `baseURL` 将自动加在 `url` 前面，除非 `url` 是一个绝对 URL。
  // 它可以通过设置一个 `baseURL` 便于为 axios 实例的方法传递相对 URL
  baseURL: 'https://some-domain.com/api/',

  // 自定义请求头
  headers: {'X-Requested-With': 'XMLHttpRequest'},

  // `params` 是与请求一起发送的 URL 参数
  // 必须是一个简单对象或 URLSearchParams 对象
  params: {
    ID: 12345
  },

  // `data` 是作为请求体被发送的数据
  // 仅适用 'PUT', 'POST', 'DELETE 和 'PATCH' 请求方法
  // 在没有设置 `transformRequest` 时，则必须是以下类型之一:
  // - string, plain object, ArrayBuffer, ArrayBufferView, URLSearchParams
  // - 浏览器专属: FormData, File, Blob
  // - Node 专属: Stream, Buffer
  data: {
    firstName: 'Fred'
  },
  
  // 发送请求体数据的可选语法
  // 请求方式 post
  // 只有 value 会被发送，key 则不会
  data: 'Country=Brasil&City=Belo Horizonte',

  // `timeout` 指定请求超时的毫秒数。
  // 如果请求时间超过 `timeout` 的值，则请求会被中断
  timeout: 1000, // 默认值是 `0` (永不超时)

  // `proxy` 定义了代理服务器的主机名，端口和协议。
  // 您可以使用常规的`http_proxy` 和 `https_proxy` 环境变量。
  // 使用 `false` 可以禁用代理功能，同时环境变量也会被忽略。
  // `auth`表示应使用HTTP Basic auth连接到代理，并且提供凭据。
  // 这将设置一个 `Proxy-Authorization` 请求头，它会覆盖 `headers` 中已存在的自定义 `Proxy-Authorization` 请求头。
  // 如果代理服务器使用 HTTPS，则必须设置 protocol 为`https`
  proxy: {
    protocol: 'https',
    host: '127.0.0.1',
    port: 9000,
    auth: {
      username: 'mikeymike',
      password: 'rapunz3l'
    }
  }
}
```
更多的配置可以在Axios文档上面看到 [Axios中文文档](https://www.axios-http.cn/docs/req_config)
# 拦截器
拦截器的作用是在请求或响应在被then或catch处理前拦截它们  
拦截器有两种，分别为：**请求拦截器**和**响应拦截器**
```js
// 添加请求拦截器
axios.interceptors.request.use(function (config) {
    // 在发送请求之前做些什么
    return config;
  }, function (error) {
    // 对请求错误做些什么
    return Promise.reject(error);
  });

// 添加响应拦截器
axios.interceptors.response.use(function (response) {
    // 2xx 范围内的状态码都会触发该函数。
    // 对响应数据做点什么
    return response;
  }, function (error) {
    // 超出 2xx 范围的状态码都会触发该函数。
    // 对响应错误做点什么
    return Promise.reject(error);
  });
```
如果需要移除拦截器，可以使用eject方法
```js
const myInterceptor = axios.interceptors.request.use(function () {/*...*/});
axios.interceptors.request.eject(myInterceptor);
```
需要注意的是，如果有多个拦截器共同使用，请求拦截器和响应拦截器的响应时机会有所不同，具体为：
* 请求拦截器，越晚声明，越早执行
* 响应拦截器，越晚声明，越晚执行
# 取消请求
axios提供了两种取消请求的方式，分别为：
* AbortController
* CancelToken
但是在新版本的Axios中CancelToken被弃用了，Axios也推荐我们使用AbortController。  
具体用法为：
```js
const controller = new AbortController();

axios.get('/foo/bar', {
   signal: controller.signal
}).then(function(response) {
   //...
});
// 取消请求
controller.abort()
```
也就是说，需要创建一个`AbortController`实例，并且在axios的配置对象中设置`signal`为`实例上的signal`方法，如果需要取消请求，就可以调用`controller.abort()`就可以了。  
该方法的原理是使用了AbortController方法，可以去了解一下。[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/AbortController)