## 1 .1.1安装

```js
//初始化，并生成package.json文件
npm init -y
//安装express
npm i express --save
```



## 1.1.2 HelloWorld

```js
let express = require('express');
//相当于http.createServer()
let app = express();

app.get('/', (req, res)=>{
    res.send('Hello World, index.page');
});

app.listen(3000, ()=>{
    console.log('server is running in 3000');
});

```



## 1.1.3 修改代码后自动重启

```js
//全局安装nodemon
npm i nodemon --global

//启动项目命令
nodemon app.js
```



## 1.1.4基本路由

```js
//get
app.get('/login', (req, res)=>{
    res.send('login.page');
});

// 动态路由
app.get('/user/:id', (req, res)=>{
    console.log(id);
    res.send('login.page');
});

//post
app.post('/pushImg', (req, res)=>{
    res.send('pushImg.page');
});
```



## 1.1.5管理静态资源

```js
//当url以 /static 开头时， 去 ./public 路径中查找文件
app.use('/static', express.static('./public'));

//省略第一个参数的时候，路由为/时，去 ./public 路径中查找文件
app.use(express.static('./public'));
```



## 1.2.1使用中间件

```js
const express = require('express');
const app = new express();

// 应用中间件，放在路由前面。
// 每一次访问都会经过这里，调用next() 往下执行，不调用则不再往下执行。可以在这里做权限判断
app.use((req, res, next) => {
    const {isAdmin} = req.query;
    if(isAdmin){
        next();
    }
})

app.get('/', (req, res) => {
    res.send('Hello Express')
})

// 错误处理中间件（其实就是放在路由后面，当所有的路由都不匹配时，进入该中间件）
app.use((req, res) => {
    res.send('Not Found')
})


app.listen(3000)
```

使用中间件设置允许跨域

```js
//设置跨域访问
app.all("*",(req,res,next)=>{
    //设置允许跨域的域名，*代表允许任意域名跨域
    res.header("Access-Control-Allow-Origin","*");
    //允许的header类型
    res.header("Access-Control-Allow-Headers","content-type");
    //跨域允许的请求方式
    res.header("Access-Control-Allow-Methods","DELETE,PUT,POST,GET,OPTIONS");
    if (req.method.toLowerCase() === 'options')
        res.send(200);  //让options尝试请求快速结束
    else{
        next();
    }
})
```



## 1.3.1获取get请求传过来的参数

```js
//使用req.query获取get请求传过来的参数，类型为Object
app.get('/pinglun', (req, res)=>{
    console.log(req.query);
    let comment = req.query;
    comments.unshift(comment);
    //重定向回首页
    res.redirect('/');
});
```



## 1.3.2获取post请求传过来的参数

在Express中没有内置的获取POST请求体的Api，我们需要使用一个第三方包 `body-parser`

安装：

```js
npm i body-parser --save
```

配置：

```js
let express = require('express')
//1.引包
let bodyParser = require('body-parser')
let app = express()

//2.加入这个配置后，req请求对象上回多出一个属性：body
//我们可以通过req.body来获取POST请求体的数据
// parse application/x-www-form-urlencoded 前端以form表单格式传递参数
app.use(bodyParser.urlencoded({ extended: false }))
// parse application/json 前端以json格式传递参数
app.use(bodyParser.json())

app.post('/login', (req, res) => {
    // 用req.body接受传递过来的参数
    const {username, password} = req.body;
    res.send({
        code: 200,
        date: {
            username,
            password
        }
    })
})
```



## 1.4.1从文件中读取数据

Express读文件还是使用原生的fs模块，读取到的内容时2进制。

假设现在有个db.json文件

```json
{
    "students": [
        {"id": 1, "name": "张三", "age": 18, "sex": 0},
        {"id": 2, "name": "张三", "age": 18, "sex": 0},
        {"id": 3, "name": "张三", "age": 18, "sex": 0},
        {"id": 4, "name": "张三", "age": 18, "sex": 0},
        {"id": 5, "name": "张三", "age": 18, "sex": 0},
        {"id": 6, "name": "张三", "age": 18, "sex": 0}
    ]
}
```

想要读取，并得到students这个数组

```js
//引入fs模块
let fs = require('fs');

//读取文件
app.get('/db', (req, res)=>{
    fs.readFile('./public/db.json', function (err, data) {
        if(err){
            return res.send('server error');
        }
        //得到的data为二进制，需要转换为字符串
        let str = data.toString();
        //将json字符串转换为js对象
        let obj = JSON.parse(str);
        console.log(obj.students);
    })
});
```



## 1.4.2写入文件

实现：复制db.json文件，写入到newFile.json文件

```js
//需要用到fs.writeFile
fs.writeFile(写入的文件路径, 写入的内容, function(err){});
//当写入的文件路径指向的文件已存在时，写入的内容将完全覆盖原来的内容
```



```js
router.get('/writeFile', (req, res)=>{
    fs.readFile('./public/db.json', (err, data)=>{
        if(err){
            return res.send('server error');
        }

        fs.writeFile('./public/newFile.json', data, (err)=>{
            if(err){
                res.send('error');
            }else {
                res.send('success');
            }
        });
    });

});
```



## 1.5.1封装路由

1.创建一个router.js文件专门管理路由

```js
let express = require('express');
//1.创建一个路由容器
let router = express.Router();

router.use((req, res, next) => {
	console.log('======')
	console.log('indexRouter 拦截器')
	console.log('======')
    next()
})

//2.把路由挂载到路由容器中
router.get('/', (req, res) => {
    res.render('index.html', {
        comments
    });
});

router.get('/post', (req, res) => {
    res.render('post.html')
});

router.post('/postComment', (req, res)=>{
    console.log(req.body);
    let commnet = req.body;
    comments.unshift(commnet);
    res.redirect('/');
});

//把router导出去，给app.js引入并获取router对象
module.exports = router;

```



2.在app.js中引入router.js

```JS
let express = require('express');
//1.引入router.js
let router = require('./router');
let app = express();


//2.把路由器加载到服务中
app.use('/indexRouter', router);


app.listen(3000, () => {
    console.log('server is running in 3000');
});

```



