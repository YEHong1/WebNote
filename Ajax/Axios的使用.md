## 1.GET方式发送请求

```js
axios.get('http://localhost:3000/posts', {
    // 携带参数 id=1
    params: {
        id: 1
    },
    // 设置token请求头
    headers: {
        "token": "123"
    }
}).then(res=>{
    console.log(res.data)
})
```



## 2.POST方式发送请求

```js
// 第一个参数为请求地址  第二个参数为要传递的参数， 第三个参数设置请求头
axios.post('http://localhost:3000/posts', {
    id: 3,
    title: 'json-serer3',
    author: 'typicode3'
}, {
    headers: {
        "Content-Type": "application/json",
        "token": '123'
    }
}).then(res=>{
    console.log(res.data)
})
```





## 3.执行并发请求

```js
function getPosts() {
    return axios.get('http://localhost:3000/posts', {
        params: {
            id: 1
        },
        headers: {
            "token": "123"
        }
    })
}

function getComments() {
    return axios.get('http://localhost:3000/comments', {
        headers: {
            "token": "123"
        }
    })
}

axios.all([getPosts(), getComments()]).then(res=>{
    //res是一个数组，时两个请求对应的响应体的集合
    console.log(res)
})
```