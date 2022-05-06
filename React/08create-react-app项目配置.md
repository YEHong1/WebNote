## 创建脚手架项目指令

```js
// 创建普通（JavaScript）项目
npx create-react-app 项目名称

// 创建 TypeScript 项目
npx create-react-app 项目名称 --template typescript
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



## 3.以`HTTPS`启动项目

某些`SDK`只在 `https` 的网站上生效，我们可以这样做

```js
// 在项目根路径创建 .env文件

// 启用HTTPS
HTTPS=true
// 修改端口号
PORT=3600
// 是否开启sourcemap
GENERATE_SOURCEMAP=false
```



## 4.按需加载`Antd`，配置less、路径别名

配置预处理器有两种方法

- 运行 npm run eject， 把webpack暴露出来， 不建议这种操作
- 新建一个文件来覆盖 `webpack`配置，下面介绍的就是这种 （ `craco` ）

```js
1.安装依赖包
yarn add @craco/craco craco-less


2.在项目根目录下创建 craco.config.js 文件
const CracoLessPlugin = require('craco-less');
const { resolve } = require('path');

module.exports = {
	plugins: [
		{
			// 这里是配置less的
			plugin: CracoLessPlugin,
			// 下面这块是配置antd的按需引入
			options: {
				lessLoaderOptions: {
					lessOptions: {
						// 这里利用了 less-loader 的 modifyVars 来进行主题配置
						modifyVars: { '@primary-color': '#1DA57A' },
						javascriptEnabled: true,
					},
				},
			},
		},
	],
	webpack: {
		// 配置路径别名
		alias: {
			'@': resolve("src")
		}
	}
};



3.修改package.json 中的 script 指令
 "scripts": {
    "start": "craco start",
    "build": "craco build",
    "test": "craco test",
 },
```



## 5.移动端适配（vw）

```js
// 也是在craco的基础上配置
1. yarn add postcss-px-to-viewport

2.在 craco.config.js 添加 style 配置
const pxToViewport = require('postcss-px-to-viewport')

module.exports = {
	webpack: {},
  style: {
		postcss: {
			mode: 'extends',
			loaderOptions: {
				postcssOptions: {
					ident: 'postcss',
					plugins:[
						pxToViewport({
							// 视口宽度(设计稿宽度)
							viewportWidth: 375
						})
					]
				}
			}
		}
	}
};
```

