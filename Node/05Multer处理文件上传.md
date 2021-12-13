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
        <!-- 文件选择器(单选)  -->
        <p><input type="file" id="fileDom1" name="avatar"></p>
        <!-- 文件选择器(多选) -->
        <p><input type="file" id="fileDom2" name="file" multiple></p>
        <p id="btn1">提交单选文件</p>
        <p id="btn2">提交多选文件</p>
        <p id="btn3">提交多个文件选择器选择的文件</p>
    </form>

<script src="axios.min.js"></script>
<script>

    let btn1 = document.getElementById('btn1');
    let btn2 = document.getElementById('btn2');
    let btn3 = document.getElementById('btn3');


    btn1.onclick = ()=>{
        let formData = new FormData();
        let fileDom = document.getElementById('fileDom1');
        formData.append('name', '张三');
        formData.append('age', '18');
        formData.append('avatar', fileDom.files[0]);
        // 直接使用axios.post()会在调试器中看到formData为空对象，这貌似是axios拦截器的问题，像下面这种写法就没问题
        axios({
            url: '/upload_one_file',
            method: 'post',
            data: formData
        }).then(res=>{
            console.log(res);
        })
    };

    btn2.onclick = ()=>{
        let formData = new FormData();
        let file = document.querySelector('#fileDom2').files;
        for(let i=0; i<file.length; i++){
            formData.append(`imgArr`, file[i]);
        }

        axios({
            url: '/upload_fileArr',
            method: 'post',
            data: formData
        }).then(res=>{
            console.log(res);
        })
    };

    btn3.onclick = ()=>{
        let formData = new FormData();
        let file1 = document.querySelector('#fileDom1').files[0];
        let file2 = document.querySelector('#fileDom2').files;
        formData.append('avatar', file1);
        // formDate可以append相同的名称
        for(let i=0; i<file2.length; i++){
            formData.append('imgArr', file2[i])
        }

        axios({
            url: '/upload_more_file',
            method: 'post',
            data: formData
        }).then(res=>{
            console.log(res);
        })
    };

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
        cb(null, originalName + '-' + Date.now() + extname);
    }
});

let upload = multer({ storage });

// 处理单文件上传
router.post('/upload_one_file', upload.single('avatar'), (req, res) => {
    res.send({
        code: 0,
        data: res.file
    })
});

// 处理多个同名文件
router.post('/upload_fileArr', upload.array('imgArr', 10), (req, res) => {
    console.log(req.files);
    res.send('Hello')
});

// 处理多个不同命文件
let cpUpload = upload.fields([{name: 'avatar', maxCount: 1}, {name: 'imgArr', maxCount: 6}]);
router.post('/upload_more_file', cpUpload, (req, res) => {
    console.log(req.files);
    res.send('Hello')
});

module.exports = router;

```

