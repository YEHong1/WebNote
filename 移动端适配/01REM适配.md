GitHub地址： https://github.com/amfe/lib-flexible

## 使用：

```shell
npm i -S amfe-flexible
```

```html
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, minimum-scale=1, user-scalable=no">
<script src="./node_modules/amfe-flexible/index.js"></script>
```

这个包会根据设备大小自动设置根元素字体大小



# `postcss-pxtorem`

 这是一款 `postcss` 插件，用于将单位转化为 rem。这样开发者在代码中直接写`px`即可。

```shell
npm install postcss-pxtorem --save-dev
```

配置

在项目根目录创建 `postcss.config.js`

```js
// `postcss.config.js`
module.exports = {
    plugins: {
        autoprefixer: {
            browsers: ['Android >= 4.0', 'iOS >= 8'],
        },
        'postcss-pxtorem': {
            rootValue: 37.5,
            propList: ['*'],
        },
    },
};

```

 