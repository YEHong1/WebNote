## 1.axios简介

 Axios 是一个基于 promise 的 HTTP 库，可以用在浏览器和 node.js 中。 

特性：

-   在浏览器端中创建 Ajax请求
-   从 node.js 创建 HTTP请求
-   支持Promise Api
-   可以拦截请求和响应
-   可以转换请求数据和响应数据
-   取消请求
-   自动转换 JSON 数据
-   客户端支持防御 XSRF（ 跨站请求伪造 ）



## 2.基本使用

```js
// 发送GET请求
axios.get('/getUserById', {
    // 参数
    params: {id: 1},
    // 请求头
    headers: {}
}).then(res=>{
    console.log(res)
}).catch(err=>{
    console.log(err)
})

// 发送POST请求
axios.post('/setUserById', { id: 1 }, {
    headers: {}
}).then(res=>{
    console.log(res)
}).catch(err=>{
    console.log(err)
})
```

处理并发请求

```js
// 有时候我们想要同时执行多个请求，在请求都完成后再调用回调函数
axios.all([request1, request2]).then(res=>{
    console.log(res) // 返回一个数组，数组中顺序为axios的all接受数组的顺序
})
```





## 3.Axios函数形式发送请求

 可以通过向 `axios` 传递相关配置来创建请求 

```js
// axios.post、axios.get这类写法是axios()的语法糖
axios({
    // get请求时可以省略method
    method: 'post',
    url: '/user/12345',
    // post请求用data
    data: {},
    // get请求用params
    params: {}
});
```

### 请求配置

这些是创建请求时可以用的配置选项。只有 `url` 是必需的。如果没有指定 `method`，请求将默认使用 `get` 方法。 

```js
let config = {
    // `url` 是用于请求的服务器 URL
    url: '/user',
    // `method` 是创建请求时使用的方法
    method: 'get', // default
    // `baseURL` 将自动加在 `url` 前面，除非 `url` 是一个绝对 URL。
    // 它可以通过设置一个 `baseURL` 便于为 axios 实例的方法传递相对 URL
    baseURL: 'https://some-domain.com/api/',
    // `transformRequest` 允许在向服务器发送前，修改请求数据
    // 只能用在 'PUT', 'POST' 和 'PATCH' 这几个请求方法
    // 后面数组中的函数必须返回一个字符串，或 ArrayBuffer，或 Stream
    transformRequest: [function (data, headers) {
        // 对 data 进行任意转换处理
        return data;
    }],
    // `transformResponse` 在传递给 then/catch 前，允许修改响应数据
    transformResponse: [function (data) {
        // 对 data 进行任意转换处理
        return data;
    }],
    // `headers` 是即将被发送的自定义请求头
    headers: {'X-Requested-With': 'XMLHttpRequest'},
    // `params` 是即将与请求一起发送的 URL 参数
    // 必须是一个无格式对象(plain object)或 URLSearchParams 对象
    params: {
        ID: 12345
    },
    // `data` 是作为请求主体被发送的数据
    // 只适用于这些请求方法 'PUT', 'POST', 和 'PATCH'
    data: {
        firstName: 'Fred'
    },
    // `timeout` 指定请求超时的毫秒数(0 表示无超时时间)
    // 如果请求花费了超过 `timeout` 的时间，请求将被中断
    timeout: 1000,
    // `responseType` 表示服务器响应的数据类型，
    // 可以是 'arraybuffer', 'blob', 'document', 'json', 'text', 'stream'
    responseType: 'json', // default
    // `onUploadProgress` 允许为上传处理进度事件
    onUploadProgress: function (progressEvent) {
        // Do whatever you want with the native progress event
    },
    // `onDownloadProgress` 允许为下载处理进度事件
    onDownloadProgress: function (progressEvent) {
        // 对原生进度事件的处理
    },
    // `maxContentLength` 定义允许的响应内容的最大尺寸
    maxContentLength: 2000,
    // `validateStatus` 定义对于给定的HTTP 响应状态码是 resolve 或 reject  promise 。
    // 如果 `validateStatus` 返回 `true` (或者设置为 `null` 或 `undefined`)，promise 将被 resolve; 	  // 否则，promise 将被 rejecte
    validateStatus: function (status) {
        return status >= 200 && status < 300; // default
    },
    // `cancelToken` 指定用于取消请求的 cancel token
    // （查看后面的 Cancellation 这节了解更多）
    cancelToken: new CancelToken(function (cancel) {
    })
}
```

### 请求响应数据格式

```js
{
  // `data` 由服务器提供的响应
  data: {},
  // `status` 来自服务器响应的 HTTP 状态码
  status: 200,
  // `statusText` 来自服务器响应的 HTTP 状态信息
  statusText: 'OK',
  // `headers` 服务器响应的头
  headers: {},
   // `config` 是为请求提供的配置信息
  config: {},
  // 'request'
  // 浏览器端是 XMLHttpRequest 实例
  request: {}
}
```



## 4.自定义axios实例

有时候我们的项目可能要用到多个服务器的接口，这时候可以创建一个自定义实例来进行分类，设置默认参数、请求拦截，从而减少代码量

```js
const instance = axios.create({
  baseURL: 'https://api.example.com'
});

// 需要注意的是：使用axios.create创建的实例没有 取消请求的功能，也不能使用 axios.all
```



## 5.配置请求默认信息

### 全局的 axios 默认值

```js
axios.defaults.baseURL = 'https://api.example.com';
axios.defaults.headers.common['Authorization'] = AUTH_TOKEN;
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded';
```

### 自定义实例默认值

```js
const instance = axios.create({
  baseURL: 'https://api.example.com'
});

instance.defaults.headers.common['Authorization'] = AUTH_TOKEN;
```

### 配置的优先顺序

请求的config配置 > 默认配置



## 6.取消请求

 通过传递一个 executor 函数到 `CancelToken` 的构造函数来创建 cancel token 

```js
let cancelToken;
axios.get('/getUserById', {
    params: {id: 1},
    headers: {},
    cancelToken: new axios.CancelToken(c=>{
        // c是一个函数，调用c可以取消请求
        cancelToken = c;
    })
}).then(res=>{
    cancelToken = null
    console.log(res)
}).catch(err=>{
    cancelToken = null
    console.log(err)
})

setTimeout(()=>{
    if(cancelToken){
        console.log('取消请求')
        cancelToken('手动取消请求');
        cancelToken = null
    }
}, 3000)
```



## 7.拦截器（重点）

拦截器会在.then 或 .catch 前执行，用来处理相同的逻辑

axios.create()创建的实例也可以设置拦截器

### 请求拦截器

```js
// 注意： 后定义的请求拦截器先执行
axios.interceptors.request.use(
    config=>{
        // 在发送请求之前做些什么
        // 例如 loading界面、添加token信息
        console.log('拦截器1 发送请求')
        return config
    },
    error => {
        // 对请求错误做些什么
        console.log('拦截器1 请求错误处理')
        return error
    }
)

axios.interceptors.request.use(
    config=>{
        // 在发送请求之前做些什么
        console.log('拦截器2 发送请求')
        return config
    },
    error => {
        // 对请求错误做些什么
        console.log('拦截器2 请求错误处理')
        return error
    }
)
```



### 响应拦截器

```js
// 响应拦截器按定义顺序执行
axios.interceptors.response.use(
    response=>{
        // 对响应数据做点什么
        console.log('拦截器1 处理响应数据')
        return response
    },
    error => {
        // 对响应错误做点什么
        console.log('拦截器1 处理响应错误')
        return error
    }
)

axios.interceptors.response.use(
    response=>{
        // 对响应数据做点什么
        console.log('拦截器2 处理响应数据')
        return response
    },
    error => {
        // 对响应错误做点什么
        console.log('拦截器2 处理响应错误')
        return error
    }
)
```



### 移除拦截器

```js
1.移除请求拦截器
const myInterceptor1 = axios.interceptors.request.use(function () {/*...*/});
axios.interceptors.request.eject(myInterceptor);

2.移除响应拦截器
const myInterceptor2 = axios.interceptors.response.use(function () {/*...*/});
axios.interceptors.response.eject(myInterceptor);
```
