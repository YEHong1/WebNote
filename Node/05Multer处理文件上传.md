## 1.简介

Multer 是一个 node.js 中间件，用于处理 `multipart/form-data` 类型的表单数据，它主要用于上传文件。

**注意**: Multer 不会处理任何非 `multipart/form-data` 类型的表单数据。



## 2.Express中使用



前端使用formData发送数据

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<form>
    <p>
        <input type="file" id="fileDom" name="file">
    </p>
    <p id="btn">提交</p>
</form>

<script src="axios.min.js"></script>
<script>

    let btn = document.getElementById('btn');

    btn.onclick = function(){
        let formData = new FormData();
        let fileDom = document.getElementById('fileDom');
        formData.append('name', '张三');
        formData.append('age', '18');
        formData.append('avatar', fileDom.files[0])
        // 直接使用axios.post()会在调试器中看到formData为空对象，这貌似是axios拦截器的问题，像下面这种写法就没问题
        axios({
            url: '/login',
            method: 'post',
            data: formData
        }).then(res=>{
            console.log(res);
        })
    }

</script>
</body>
</html>
```



Express中

```js
let express = require('express');
let router = express.Router();
const path = require('path');
const multer  = require('multer');
const mkdirp = require('mkdirp');
let moment = require('moment');

let storage = multer.diskStorage({
    // 配置上传目录, 分日期
    destination: function (req, file, cb) {
        let date = moment(Date.now()).format('YYYY-MM-DD');
        let pathStr = `public/${date}/`;
        // 用mkdirp模块创建文件夹
        mkdirp(pathStr).then(res=>{
            cb(null, pathStr)
        })

    },
    // 配置文件名
    filename: function (req, file, cb) {
        // 获取上传文件的原名
        let originalName = file.originalname;
        // 获取文件扩展名
        let extname = path.extname(originalName);
        cb(null, file.fieldname + '-' + Date.now() + extname);
    }
})

let upload = multer({ storage })

/* GET home page. */
router.get('/', function (req, res, next) {
    res.render('index', {title: 'Express'});
});

// 单个文件上传用 single(fieldname) ，参数为文件名，也就是上面配置文件名的file.filedname
router.post('/login', upload.single('avatar'), (req, res) => {
    console.log(req.file)
    res.send('Hello')
})

module.exports = router;

```

