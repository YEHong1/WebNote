## 创建脚手架项目指令

```js
// 创建普通（JavaScript）项目
npx create-react-app 项目名称

// 创建 TypeScript 项目
npx create-react-app 项目名称 --template t
```



## 1.配置代理

### 法一

```json
// 直接再package.json 中 添加 proxy 属性
// 这种方法只能配置一个代理。 假设当前的服务是3000 ，需要代理到5000。发起请求时，会先去3000找，找不到再去5000找
{
    "proxy": "http://192.168.10.57:5000"
}
```



### 法二

```js
// 通过middleware中间件的方式设置proxy
// 1.npm install http-proxy-middleware --save， (脚手架已经默认下载了)
// 2.在src目录下创建一个名为 setupProxy.js 的文件，代码如下

// 注意，这里引入不能用import，要用require
const { createProxyMiddleware } = require('http-proxy-middleware')

module.exports = function (app) {
  app.use(createProxyMiddleware('/api', {
    // 代理目标服务
    target: 'http://172.16.136.249:8080',
    secure: false,
    changeOrigin: true,
    // 路径替换，这里是把 /api 替换为 空串
    pathRewrite: {
      "^/api": ""
    }
  }))
}
```



## 2.修改端口号

```js
// 将package.json 中 的启动命令修改一下
"start": "react-scripts start"

// 修改后
"start": "set PORT=8082 && react-scripts start",
```



## 3.按需加载Antd，配置less、路径别名

配置预处理器有两种方法

- 运行 npm run eject， 把webpack暴露出来， 不建议这种操作
- 新建一个文件来覆盖 webpack配置，下面介绍的就是这种

```js
1.安装依赖包
yarn add react-app-rewired customize-cra babel-plugin-import less less-loader antd

注意：如果出现这样的报错提示 this.getOptions is not a function，是因为less-loader的版本过高，可以尝试安装 less-loader@5.0.0

2.在项目根目录下创建config-overrides.js文件
const { override, fixBabelImports, addLessLoader, addWebpackAlias } = require("customize-cra");
const path = require("path");

module.exports = override(
  // 针对antd 实现按需打包：根据import来打包 (使用babel-plugin-import)  
  fixBabelImports("import", {    
    libraryName: "antd",    
    libraryDirectory: "es",    
    style: true, // 自动打包相关的样式 默认为 style:'css'  
  }),
  // 使用less-loader对源码中的less的变量进行重新制定，设置antd自定义主题  
  addLessLoader({  
    javascriptEnabled: true,    
    modifyVars: { "@primary-color": "#1DA57A" },  
  }),
  //增加路径别名的处理 
  addWebpackAlias({  
    '@': path.resolve('./src')  
  })
); 


3.修改package.json 中的 script 指令
 "scripts": {
    "start": "react-app-rewired start",
    "build": "react-app-rewired build",
    "test": "react--app-rewired test",
    "eject": "react-scripts eject"
 },
```



## 4.以HTTPS启动项目

某些SDK只在 https 的网站上生效，我们可以这样做

```js
// 在项目根路径创建 .env文件

// 启用HTTPS
HTTPS=true
// 修改端口号
PORT=3600
// 是否开启sourcemap
GENERATE_SOURCEMAP=false
```

