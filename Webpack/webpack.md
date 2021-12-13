# 第 1 章：webpack 简介



## 1.1 webpack 是什么

-   webpack 是一种前端资源构建工具，一个静态模块打包器（modulebundler）
-    在 webpack 看来, 前端的所有资源文件(js/json/css/img/less/...)都会作为模块处理（chunk）
-   它将根据模块的依赖关系进行静态分析，打包生成对应的静态资源（bundle）

```js
// module，chunk 和 bundle 其实就是同一份逻辑代码在不同转换场景下的取了三个名字：
// 我们直接写出来的是 module，webpack 处理时是 chunk，最后生成浏览器可以直接运行的 bundle
```



## 1.2 webpack 五个核心概念

### 1.2.1Entry

入口(Entry)指示 webpack 以哪个文件为入口起点开始打包，分析构建内部依赖图。

### 1.2.2Output

输出(Output)指示 webpack 打包后的资源 bundles 输出到哪里去，以及如何命名。

### 1.2.3Loader

Loader 让 webpack 能够去处理那些非 JavaScript 文件(webpack 自身只理解 JavaScript / Json)

### 1.2.4Plugins

插件(Plugins)可以用于执行范围更广的任务。插件的范围包括，从打包优化和压缩，一直到重新定义环境中的变量等。

### 1.2.5Mode

模式(Mode)指示 webpack 使用相应模式的配置。

| 选项        | 描述                                                         | 特点                        |
| ----------- | ------------------------------------------------------------ | --------------------------- |
| development | 会将 DefinePlugin 中 process.env.NODE_ENV 的值设置 为 development。<br/>启用 NamedChunksPlugin 和 NamedModulesPlugin。 | 能让代码本地调试 运行的环境 |
| production  | 会将 DefinePlugin 中 process.env.NODE_ENV 的值设置 为 production。<br/>启用 FlagDependencyUsagePlugin, FlagIncludedChunksPlugin,ModuleConcatenationPlugin, NoEmitOnErrorsPlugin,OccurrenceOrderPlugin, SideEffectsFlagPlugin 和 TerserPlugin。 | 能让代码优化上线 运行的环境 |

# 第 2 章：webpack 的初体验



## 2.1 初始化配置

1.  ### 初始化 package.json 	

    ```
    npm init -y
    ```

    

2.  ### 下载并安装 webpack 

    ```js
    // 全局安装,方便使用指令
    npm i webpack webpack-cli -g
    // 给项目添加开发依赖
    npm i webpack webpack-cli -D
    ```
    
    ```js
    // 注意版本问题，记录笔记时版本如下
    "webpack": "^4.46.0",
    "webpack-cli": "^3.3.12",
    "webpack-dev-server": "^3.11.2"
    
    ```
    
    



## 2.2 编译打包应用

webpack默认只能处理js和json资源，需要处理其他资源需要使用其他loader

```js
//1.创建文件

//2.运行指令 webpack 入口文件路径及文件名 -o 出口文件路径及文件名 --mode=模式

//开发模式例子
webpack ./src/index.js -o ./build/main.js --mode=development
//功能：webpack 能够编译打包 js 和 json 文件，并且能将 es6 的模块化语法转换成浏览器能识别的语法。

//生产模式
webpack ./src/index.js -o ./build/main.js --mode=production
//功能：在开发配置功能上多一个功能，压缩代码。

//3.结论：
//webpack能够编译打包JavaScript 和 Json 文件。
//能将es6模块化语法转换为浏览器能识别的模块化语法
//能压缩代码

//4.问题
//不能编译打包css、img等文件
//不能将es6基本语法转换为es5以下语法

```



# 第 3 章：webpack 开发环境的基本配置



## 3.1 创建配置文件

```js
//1.创建webpack配置文件 webpack.config.js
//作用: 指示 webpack 干哪些活（当你运行 webpack 指令时，会加载里面的配置）
//所有构建工具都是基于nodejs平台运行的~模块化默认采用commonjs。

//2.配置内容模板

//引入node内置模块，resolve用来拼接绝对路径的方法
const {resolve} = require('path');

module.exports = {
    // 入口起点
    entry: './src/index.js',
    //输出
    output: {
        //输出的文件名
        filename: 'build.js',
        //输出的路径, __dirname是当前文件目录的绝对路径
        path: resolve(__dirname, 'build')
    },
    //loader配置
    module: {
        rules: []
    },
    //plugins
    plugins: [],
    //模式
    mode: 'development' //开发模式

};

//3.运行指令为 webpack

```



## 3.2 打包样式资源

```js
// 注意：使用webpack4时，less-loader应使用@7版本
```

```js
//假设现在有index.css index.less, 在index.js中引入
//需要安装 css-loader  style-loader  less  less-loader
//在module中配置
const {resolve} = require('path');
module.exports = {
    entry: './src/index.js',
    output: {
        filename: 'build.js',
        path: resolve(__dirname, 'build')
    },
    //loader配置
    module: {
        rules: [
            //不同文件必须配置不同loader处理
            //.css文件
            {
                //正则匹配文件
                test: /\.css$/,
                //使用那些loader进行处理
                use: [
                    //use数组中loader执行顺序：从右到左，从下到上 依次执行
                    //创建style标签，将js中的样式资源插入进去，添加到head中生效
                    'style-loader',
                    //将css文件变成commonjs模块加载到js中，里面内容是样式字符串
                    'css-loader'
                ]
            },
            //.less文件
            {
                test: /\.less$/,
                use: [
                    'style-loader',
                    'css-loader',
                    // 将less文件编译成css文件
                    // 需要下载 less-loader和less
                    'less-loader'
                ]
            }
        ]
    },
    plugins: [],
    mode: 'development' 
};
```



## 3.3 打包 HTML 资源

```js
// 注意，webpack4版本的 html-webpack-plugin也需要使用@4版本
```

```js
//项目中有一个index.html文件
//准备工作： 下载安装 plugin 包  npm i html-webpack-plugin -D

//配置文件 webpack.config.js
const {resolve} = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
    entry: './src/index.js',
    output: {
        filename: 'build.js',
        path: resolve(__dirname, 'build')
    },
    module: {
        rules: []
    },
    //plugins
    plugins: [
        // html-webpack-plugin
        //功能：默认会创建一个空的HTML，自动引入打包输出的所有资源（JS/CSS）
        // new HtmlWebpackPlugin(),
        //需要有结构的HTML文件时，则需要传入配置对象
        new HtmlWebpackPlugin({
            //复制 './src/index.html' 文件，并自动引入打包输出的所有资源（JS/CSS）
            template: './src/index.html'
        })
    ],
    mode: 'development'
};
```



## 3.4 打包图片资源

```js
// webpack4 使用 html-loader@1
const {resolve} = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
    entry: './src/index.js',
    output: {
        filename: 'built.js',
        path: resolve(__dirname, 'build')
    },
    module: {
        rules: [
            {
                test: /\.css$/,
                use: ['style-loader', 'css-loader']
            },
            //处理图片资源
            {
                test: /\.(jpg|png|gif)$/,
                //下载 url-loader file-loader
                loader: 'url-loader',
                options: {
                    //小于 8kb 的图片资源将被转换为base64格式
                    limit: 8 * 1024,
                    // 问题：因为url-loader默认使用es6模块化解析，而html-loader引入图片是commonjs
                    // 解析时会出现打包后的html文件中图片路径不正确的问题
                    // 解决：关闭url-loader的es6模块化，使用commonjs解析
                    esModule: false,
                    //图片重命名
                    //[hash:10] 使用hash值得前10位来命名
                    //[ext] 取原文件的扩展名
                    name:'[hash:10].[ext]'
                }
            },
            //处理html中img标签的图片
            {
                test: /\.html$/,
                //处理html文件的img图片（负责引入img，从而能被url-loader进行处理）
                // use: ['html-loader']
                loader: 'html-loader' //只需要加载一个loader得时候要用loader来代替use
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: './src/index.html'
        })
    ],
    mode: 'development'
};
```



## 3.5 打包其他资源

```js
//打包其他资源(除了html/js/css资源以外的资源)
const {resolve} = require('path');
module.exports = {
    entry: './src/index.js',
    output: {
        filename: "built.js",
        path: resolve(__dirname, 'build')
    },
    module: {
        rules: [
            //其他资源（不需要额外处理的资源，如字体文件）
            {
                //排除css\js\html\less资源的其他资源
                exclude: /\.(css|less|js|html|jpg|png|gif)$/,
                loader: 'file-loader'
            }
        ]
    },
    plugins: [],
    mode: 'development'
};
```



## 3.6 devserver

```js
const {resolve} = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    entry: './src/index.js',
    output: {
        filename: "built.js",
        path: resolve(__dirname, 'build')
    },
    module: {
        rules: []
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: "./src/index.html"
        })
    ],
    mode: "development",
    //开发服务器 devServer：用来自动化（自动编译，自动打开浏览器，自动刷新浏览器~~）
    //安装 npm i webpack-dev-server
    //特点：只会在内存中编译打包，不会有任何文件输出
    //启动devServer指令为：npx webpack-dev-server
    devServer: {
        //项目构建后的路径
        contentBase: resolve(__dirname, 'build'),
        //启动gzip压缩
        compress: true,
        //端口号
        port: 3000,
        //是否自动打开浏览器
        open: true，
        // 跨域代理
        proxy: {
            "/api": {
                target: "https://www.zhipin.com",
                // 路径重写
                pathRewrite: {"^/api" : ""},
                // 默认情况下，不接受运行在 HTTPS 上，且使用了无效证书的后端服务器, 需要接受修改secure为false
                secure: false
            }
        }
    }
};
```



## 3.7 总结

```js
const {resolve} = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    entry: './src/index.js',
    output: {
        filename: "js/built.js",
        path: resolve(__dirname, 'build')
    },
    module: {
        rules: [
            {
                //处理css文件
                test: /\.css$/,
                use: ['style-loader', 'css-loader']
            },
            {
                //处理less文件
                test: /\.less$/,
                use: ['style-loader', 'css-loader', 'less-loader']
            },
            {
                //处理图片
                test: /\.(png|jpg|gif)$/,
                loader: 'url-loader',
                options: {
                    limit: 8*1024,
                    name: '[hash:10].[ext]',
                    //关闭Es6模块化，因为还要处理html中的图片，而处理html中的图片使用的是CommonJs
                    esModule: false,
                    //打包后文件资源的存放文件夹
                    outputPath: 'images'
                }
            },
            {
                //处理html中的图片
                test: /\.html$/,
                loader: 'html-loader'
            },
            {
                //处理其他资源
                exclude: /\.(css|less|html|png|jpg|gif|js)$/,
                loader: 'file-loader',
                options: {
                    name: '[hash:10].[ext]',
                    outputPath: 'media'
                }
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: "./src/index.html"
        })
    ],
    mode: "development",
    devServer: {
        contentBase: resolve(__dirname, 'build'),
        compress: true,
        port: 3000,
        open: true
    }
};
```



# 第4章：webpack生产环境的基本配置



## 4.1 提取css成单独文件

>   style-loader 是创建一个style标签，将css放进去
>
>   css-loader 是将css文件整合到js文件中
>
>   如果我们想把css文件提取成单独文件，需要安装一个插件
>
>   npm i --save mini-css-extract-plugin



```js
const {resolve} = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
    entry: './src/index.js',
    output: {
        filename: "js/built.js",
        path: resolve(__dirname, 'build')
    },
    module: {
        rules: [
            {
                //处理css文件
                test: /\.css$/,
                use: [
                    //style-loader是创建一个style标签，然后间css放进去
                    // 'style-loader',
                    //取代style-loader,这个loader是提取js文件中的css成单独文件
                    MiniCssExtractPlugin.loader,
                    //css-loader是将css文件整合到js中
                    'css-loader'
                ]
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: "./src/index.html"
        }),
        //需要先实例化才能使用 MiniCssExtractPlugin.loader
        new MiniCssExtractPlugin({
            //对输出的css文件进行重命名
            filename: 'css/built.css'
        })
    ],
    mode: "development"
};
```



## 4.2 css兼容处理

需要使用 postcss-loader@4 、postcss-preset-env@6（postcss-loader的插件）

postcss-preset-env 帮 postcss找到package.json中browserslist里面的配置，通过配置加载指定的css兼容性样式

在package.json中配置browserslist

```js
// pageage.json
{
    "browserslist": {
        // 开发环境
        // 默认是使用生产环境的配置，如果需要使用开发环境的配置需要设置node环境变量：
        // 在webpack.config.js中添加 process.env.NODE_ENV = 'development'
        "development": [
            //兼容最近版本的浏览器
            "last 1 chrome version",
            "last 1 firefox version",
            "last 1 safari version"
        ],
        // 生产环境：默认是看生产环境
        "production": [
            ">0.1%",
            "not dead",
            "not op_mini all"
        ]
    }
}
```

在webpack.config.js同级目录创建 postcss.config.js

```js
// postcss.config.js
module.exports = {
	plugins: [
		require('postcss-preset-env')
	]
}
```



```js
const { resolve } = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
	entry: './src/js/index.js',
	output: {
		filename: "js/built.js",
		path: resolve(__dirname, 'dist')
	},
	module: {
		rules: [
			{
				test: /\.css$/,
				use: [
					MiniCssExtractPlugin.loader,
					'css-loader',
                    // postcss配置
					{
						loader: 'postcss-loader'
					}
				]
			},
		]
	},
	plugins: [
		new HtmlWebpackPlugin({
			template: './src/index.html'
		}),
		new MiniCssExtractPlugin({
			filename: 'css/built.css'
		})
	],
	mode: 'development',
}

```



## 4.3 压缩css

>   压缩css需要安装 optimize-css-assets-webpack-plugin
>
>   npm i --save optimize-css-assets-webpack-plugin

```js
const {resolve} = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin');

module.exports = {
    entry: './src/index.js',
    output: {
        filename: "js/built.js",
        path: resolve(__dirname, 'build')
    },
    module: {
        rules: [
            { 
                test: /\.css$/,
                use: [
                    MiniCssExtractPlugin.loader,
                    'css-loader',
                    {
                        loader: 'postcss-loader'
                    }
                ]
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: "./src/index.html"
        }),
        new MiniCssExtractPlugin({
            // 对输出的css文件进行重命名
            filename: 'css/built.css'
        }),
        //压缩css
        new OptimizeCssAssetsWebpackPlugin(),
    ],
    mode: "production",
};
```



## 4.4 js语法检查

>   使用 eslint 需要下载以下的包
>
>   eslint-config-airbnb-base 不包含react语法检查，可以取npm 搜索 airbnb（这是一个eslint推荐规范）
>
>    npm install --save-dev eslint-loader eslint eslint-config-airbnb-base eslint-plugin-import 

```js
// package.json
{
    // 配置eslintConfig，设置检查规范
    "eslintConfig": {
        "extends": "airbnb-base"
    }
}
```



```js
const {resolve} = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin');

module.exports = {
    entry: './src/index.js',
    output: {
        filename: "js/built.js",
        path: resolve(__dirname, 'build')
    },
    module: {
        rules: [
            // 语法检查需要在js兼容处理前执行，设置 enforce: 'pre' 确保优先执行
            {
                test: /\.js$/,
                // 不检查node_module目录下的js
                exclude: /node_modules/,
                loader: 'eslint-loader',
                // 优先执行
        		enforce: 'pre',
                options: {
                    // 自动修复 eslint 的错误
                    fix: true
                }
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: "./src/index.html"
        }),
    ],
    mode: "production",
};
```



## 4.5 js兼容处理

>    npm install --save-dev babel-loader @babel/core @babel/preset-env @babel/polyfill  core-js 
>
>    @babel/preset-env 这个包只能处理基本语法，不能处理如Promise等高级语法
>
>    @babel/polyfill 可以处理所有js语法，但是需要配合按需加载使用，不然打包后体积会比较大

```js
// "@babel/core": "^7.13.10",
// "@babel/polyfill": "^7.12.1",
// "@babel/preset-env": "^7.13.10",
// "babel-loader": "^8.2.2",
// "core-js": "^3.9.1",

const {resolve} = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    entry: './js/index.js',
    output: {
        filename: "js/built.js",
        path: resolve(__dirname, 'build')
    },
    module: {
        rules: [
            {
                test: /\.js$/,
                // 不处理其他第三方的js文件
                exclude: /node_modules/,
                loader: 'babel-loader',
                options: {
                    // 预设：指示 babel 做怎么样的兼容性处理
                    presets: [
                        [
                            '@babel/preset-env',
                            {
                                // 按需加载
                                useBuiltIns: 'usage',
                                // 指定 core-js 版本
                                corejs: {
                                    version: 3
                                },
                                // 指定兼容性做到哪个版本浏览器
                                targets: {
                                    chrome: '60',
                                    firefox: '60',
                                    ie: '9',
                                    safari: '10',
                                    edge: '17'
                                }
                            }
                        ]
                    ]
                }
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: "./index.html"
        })
    ],
    mode: "development",
};
```





## 4.6 压缩js文件

修改mode为 production即可， 生产环境下会自动压缩 js 代码  



## 4.7 压缩html文件

```js
const { resolve } = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
    entry: './src/js/index.js',
    output: {
        filename: 'js/built.js',
        path: resolve(__dirname, 'build')
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: './src/index.html',
            // 压缩 html 代码
            minify: {
                // 移除空格
                collapseWhitespace: true,
                // 移除注释
                removeComments: true
            }
        })
    ],
    mode: 'production'
};
```



## 总结配置

```js
// package.json
{
  "name": "webpack_demo",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "npx webpack-dev-server",
    "build": "npx webpack"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@babel/core": "^7.12.10",
    "@babel/polyfill": "^7.12.1",
    "@babel/preset-env": "^7.12.11",
    "babel-loader": "^8.2.2",
    "core-js": "^3.8.3",
    "css-loader": "^5.0.1",
    "file-loader": "^6.2.0",
    "html-loader": "^1.3.2",
    "html-webpack-plugin": "^4.5.1",
    "less": "^4.1.0",
    "less-loader": "^7.2.1",
    "mini-css-extract-plugin": "^1.3.4",
    "postcss-loader": "^4.1.0",
    "postcss-preset-env": "^6.7.0",
    "url-loader": "^4.1.1",
    "webpack": "^4.46.0",
    "webpack-cli": "^3.3.12",
    "webpack-dev-server": "^3.11.2"
  },
  "browserslist": {
    "development": [
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ],
    "production": [
      ">0.1%",
      "not dead",
      "not op_mini all"
    ]
  }
}

```

```js
// postcss.config.js
module.exports = {
	// postcss-preset-env 是 postcss-loader 的 插件
	// 作用是寻找package.json中的browserslist配置
	plugins: [
		require('postcss-preset-env')
	]
}

```

```js
// webpack.config.js
const { resolve } = require('path')
// Html模板、代码压缩
const HtmlWebpackPlugin = require('html-webpack-plugin')
// 打包css成单独文件
const MiniCssExtractPlugin = require('mini-css-extract-plugin')

// css 和 less的loader 存在耦合
const commonCssLoader = [
	{
		loader: MiniCssExtractPlugin.loader,
		options: {
			// 当前的css所在的文件相对于打包后的根路径dist的相对路径
			// 这是一个注意点：不这样做可能会导致css被禁图片路径不对
			publicPath: '../'
		}
	},
	'css-loader',
	// postcss只能处理css文件，预处理器转换为css后才能执行，所以放在less的前面
	{
		loader: 'postcss-loader'
	}
]

module.exports = {
	entry: './src/js/index.js',
	output: {
		filename: 'js/main.js',
		path: resolve(__dirname, 'dist'),

	},
	module: {
		rules: [
			{
				test: /\.css$/,
				use: [...commonCssLoader]
			},
			{
				test: /\.less$/,
				use: [...commonCssLoader, 'less-loader']
			},
			{
				test: /\.(png|jpg|gif)$/,
				loader: "url-loader",
				options: {
					name: '[hash:10].[ext]',
					limit: 8 * 1024,
					esModule: false,
					outputPath: 'images'
				}
			},
			{
				test: /\.html$/,
				loader: "html-loader"
			},
			{
				test: /\.js$/,
				// 不处理其他第三方的js文件
				exclude: /node_modules/,
				loader: 'babel-loader',
				options: {
					// 预设：指示 babel 做怎么样的兼容性处理
					presets: [
						[
							'@babel/preset-env',
							{
								// 按需加载
								useBuiltIns: 'usage',
								// 指定 core-js 版本
								corejs: {
									version: 3
								},
								// 指定兼容性做到哪个版本浏览器
								targets: {
									chrome: '60',
									firefox: '60',
									ie: '9',
									safari: '10',
									edge: '17'
								}
							}
						]
					]
				}
			},
			{
				exclude: /\.(html|css|less|png|jpg|gif|js)$/,
				loader: 'file-loader'
			},
		]
	},
	plugins: [
		new HtmlWebpackPlugin({
			template: './src/index.html',
			minify: {
				// 移除注释
				removeComments: true,
				// 移除空格
				collapseWhitespace: true,
			}
		}),
		new MiniCssExtractPlugin({
			filename: 'css/main.css'
		})
	],
	mode: 'production',
	devServer: {
		contentBase: resolve(__dirname, 'dist'),
		compress: true,
		port: 3000,
		open: true
	}
}

```



# 第 5章：webpack 开发环境优化配置

开发环境优化主要有两点：

-   项目构建速度
-   项目构建后的代码调试



## 5.1 HMR（模块热替换）

在默认配置下，你更改了css或其他文件后，webpack会重新打包所有文件。

模块热替换(Hot Module Replacement 或 HMR)是 webpack 提供的最有用的功能之一, 它允许在运行时更新各种模块, 而无需进行完全刷新 

### 启动 HMR

```js
// webpack.config.js
module.exports = {
    entry: '',
    output: {},
    module: {},
    pugin: [],
    mode: 'development',
    devServer: {
		contentBase: resolve(__dirname, 'dist'),
		compress: true,
		port: 3000,
		open: true,
		// 启动 HRM
		hot: true
	}
}
```

启动 HMR 后，对于css文件，我们在开发环境下不将它打包成单独文件，而是继续使用 style-loader 打包到html的style标签中，因为 style-loader 对 HMR 做了处理，我们可以直接使用，这样的话，我们改变css后就只会重新打包被修改的css文件

```js
// webpack.config.js
module.exports = {
    entry: '',
    output: {},
    module: {
        rules: [
            {
				test: /\.css$/,
				use: ['style-loader', 'css-loader']
			},
        ]
    },
    pugin: [],
    mode: 'development',
    devServer: {
		contentBase: resolve(__dirname, 'dist'),
		compress: true,
		port: 3000,
		open: true,
		// 启动 HMR
		hot: true
	}
}
```

启用 HMR 后, module.hot 接口就会暴露在 入口文件 index.js 中, 接下来需要在 index.js 中配置告诉 webpack 接受HMR的模块， 对于 入口文件的修改，还是会引起全部文件重新打包，对于被引入到 index.js 中的文件 HMR才有效

```js
// print.js
const sayHello = ()=>{
	console.log('Hello World 123')
}

export default sayHello;
```

  配合 module.hot 实现只打包被修改的文件

```js
// index.js
import '../style/index.css'
import '../style/main.less'
import sayHello from "./print";

console.log('index.js 执行了')

if(module.hot){
	// 告诉 webpack 接受热替换的模块
	module.hot.accept('./print', ()=>{
		sayHello();
	})
}
```

对于HTML文件是没有必要使用 HMR的，以为默认只有一个HTML文件作为模板。我们在开启 HMR 后去修改 HTML文件，发现修改后界面不会发生变化。为了解决这个问题，需要把HTML文件的路径写入到入口配置中

```js
// webpack.config.js
module.exports = {
    // HTML文件在启动 HMR 后需要加到入口文件中
    entry: ['./src/js/index.js', './src/index.html'],
    output: {},
    module: {
        rules: [
            {
				test: /\.css$/,
				use: ['style-loader', 'css-loader']
			},
        ]
    },
    pugin: [],
    mode: 'development',
    devServer: {
		contentBase: resolve(__dirname, 'dist'),
		compress: true,
		port: 3000,
		open: true,
		// 启动 HRM
		hot: true
	}
}
```



## 5.2 SourceMap （用于构建后错误查找）

SourceMap是一种映射关系。当项目运行后，如果出现错误，错误信息只能定位到打包后文件中错误的位置。如果想查看在源文件中错误的位置，则需要使用映射关系，找到对应的位置。 

```js
// webpack.config.js
module.exports = {
    entry: './src/js/index.js',
    output: {},
    module: {},
    pugin: [],
    mode: 'development',
    devServer: {},
    // SourceMap配置
    devtool: 'source-map', 
}
```

可选值：

```js
// [inline-|hidden-|eval-][nosources-][cheap-[module-]]source-map

1.source-map
// 会生成map文件，且可以获得 准确错误信息 和 源代码

2.inline-source-map
// 不会生成map文件，可以获得 准确的错误信息 和 源代码

3.hidden-source-map
// 会生成map文件， 可以获得 准确的错误信息，但是看不到源代码，看到的是构建后的代码。
// 这种主要用于不想别人看到源代码的场景

4.eval-source-map
// 不会生成外部map文件。 但每一个文件都生成对应的source-map，都在eval函数中
// 可以获得 准确的错误信息 和 源代码

5.nosources-source-map
// 会生成map文件，可以看到错误代码准确信息, 但是没有任何源代码信息

6.cheap-source-map
// 会生成map文件，可以看到错误准确信息和源代码，但是错误信息只能精确到行，不会精确到列

7.cheap-module-source-map
// 和cheap-source-map一样，此外module会将loader的source map加入


// 没有生成 map 文件的构建速度更快， 带有 hidden 、 nosource的构建后看不到源代码

速度： eval > inline > cheap
调试信息更全面： souce-map > cheap-module-source-map > cheap-source-map

// 开发环境推荐（构建速度快、调试更友好）
eval-source-map 或者 eval-cheap-module-souce-map
// 开发环境
代码需要隐藏： 
nosources-source-map 全部隐藏
hidden-source-map 只隐藏源代码，会提示构建后代码错误信息

内联（不生成map文件的）会让代码体积变大，所以在生产环境不用内联

不需要隐藏代码就用 source-map 或者 cheap-module-souce-map
```



## 5.3 oneOf

Webpack的配置可能有多个 loader，但是每个文件只能匹配一个 loader，被一个 loader 处理，因此可以使用 oneOf 唯一匹配，不需要每个文件把所有的 loader 都询问一遍，可以提高 loader 的执行效率，提高构建速度

### 注意：不能有多个配置处理同一种类型的文件（例如 js语法校验和js兼容性处理不能一起放在 oneOf 中， 可以把  js语法校验 提取到 外面 ） 

```js
module: {
    rules: [
        // eslint 处理
        {},
        {
            oneOf: [
                // 其他loader处理
            ]
        }
        
    ]
}
```

```js
// webpack.config.js
const { resolve } = require('path')
// Html模板、代码压缩
const HtmlWebpackPlugin = require('html-webpack-plugin')
// 打包css成单独文件
const MiniCssExtractPlugin = require('mini-css-extract-plugin')

// css 和 less的loader 存在耦合
const commonCssLoader = [
	{
		loader: MiniCssExtractPlugin.loader,
		options: {
			// 当前的css所在的文件相对于打包后的根路径dist的相对路径
			// 这是一个注意点：不这样做可能会导致css被禁图片路径不对
			publicPath: '../'
		}
	},
	'css-loader',
	// postcss只能处理css文件，预处理器转换为css后才能执行，所以放在less的前面
	{
		loader: 'postcss-loader'
	}
]

module.exports = {
	entry: './src/js/index.js',
	output: {
		filename: 'js/main.js',
		path: resolve(__dirname, 'dist'),
	},
	module: {
		rules: [
			{
				test: /\.js$/,
				exclude: /node_modules/,
				// 优先执行
				enforce: "pre",
				loader: 'eslint-loader',
				options: {
					fix: true
				}
			},
			{
				oneOf: [
					{
						test: /\.css$/,
						use: [...commonCssLoader]
					},
					{
						test: /\.less$/,
						use: [...commonCssLoader, 'less-loader']
					},
					{
						test: /\.(png|jpg|gif)$/,
						loader: "url-loader",
						options: {
							name: '[hash:10].[ext]',
							limit: 8 * 1024,
							esModule: false,
							outputPath: 'images'
						}
					},
					{
						test: /\.html$/,
						loader: "html-loader"
					},
					{
						test: /\.js$/,
						// 不处理其他第三方的js文件
						exclude: /node_modules/,
						loader: 'babel-loader',
						options: {
							// 预设：指示 babel 做怎么样的兼容性处理
							presets: [
								[
									'@babel/preset-env',
									{
										// 按需加载
										useBuiltIns: 'usage',
										// 指定 core-js 版本
										corejs: {
											version: 3
										},
										// 指定兼容性做到哪个版本浏览器
										targets: {
											chrome: '60',
											firefox: '60',
											ie: '9',
											safari: '10',
											edge: '17'
										}
									}
								]
							]
						}
					},
					{
						exclude: /\.(html|css|less|png|jpg|gif|js)$/,
						loader: 'file-loader'
					},
				]
			}
		]
	},
	plugins: [
		new HtmlWebpackPlugin({
			template: './src/index.html',
			minify: {
				// 移除注释
				removeComments: true,
				// 移除空格
				collapseWhitespace: true,
			}
		}),
		new MiniCssExtractPlugin({
			filename: 'css/main.css'
		})
	],
	mode: "development",
	devServer: {
		contentBase: resolve(__dirname, 'dist'),
		compress: true,
		port: 3000,
		open: true,
		// 启动 HRM
		// hot: true
	},
	devtool: 'source-map',
}

```



## 5.4 缓存

缓存主要有两个点：

-   js兼容处理（babel）缓存，再次构建时速度更快
-   文件资源缓存，加快页面响应速度

```js
// js兼容缓存
module.exports = {
    entry: '',
    output: {},
    module: {
        rules: [
            {
                test: /\.js$/,
                exclude: /node_modules/,
                loader: 'babel-loader',
                options: {
                    presets: [...配置],
                    // 设置babel缓存， 第二次构建时，会读取之前的缓存
                    cacheDirectory: true
                }
            },
        ]
    }
}
```

```js
// 文件资源的缓存
1.缓存的概念
首次进行资源请求之后，服务器在返回资源给客户端的同时，客户端保存一份资源副本（在允许缓存的情况下），当我们下次再对该资源进行请求时，则会直接使用资源副本而不会从原始服务器再次请求文档

2.缓存的好处
提高页面响应速度
减轻服务器压力

3.缓存分为强缓存和协商缓存
强缓存： 后端处理请求后，在response header中 设置 cache-control：max-age（过期时间）

协商缓存： 由服务端告诉客户端是否使用缓存

```

```js
// webpack打包时存在三种 hash值

1.hash: 每次wepack构建时会生成一个唯一的hash值。

2.chunkhash：根据chunk生成的hash值。如果打包来源于同一个chunk，那么hash值就一样
在index.js中引入的css、js等资源 和 index.js 归为一个chunk，所以他们的chunkhash是一样的

3.contenthash: 根据文件的内容生成hash值。不同文件hash值一定不一样    

使用 contenthash 值拼接文件名 作为到输出文件的文件名，后端在设置 过期时间 来进行文件缓存

// node server
const express = require('express');
const app = express();
// express.static向外暴露静态资源
// maxAge 资源缓存的最大时间，单位ms
app.use(express.static('dist', { maxAge: 1000 * 3600 }));
app.listen(3000);

// webpack.config.js
module.exports = {
    entry: '',
    output: {
        filename: 'js/built.[contenthash:10].js',
        path: resolve(__dirname, 'dist')
    },
    module: {},
    plugins: [
        new MiniCssExtractPlugin({
        	filename: 'css/built.[contenthash:10].css'
        }),
    ]
}


// 缓存过之后，我们修改了js文件，再次打包。
// 这时候会发现js因为内容变化contenthash发生变化，导致了js的文件名和之前的不一样，
// 而css内容未变化，文件名和上次一致。这时去打开页面发现js重新获取资源，而css文件使用了客户端的缓存副本
```



## 5.5 tree shaking

 tree shaking 是一种代码优化技术，它能够将无用的代码进行去除，从而减少打包后的代码体积

满足2个条件时就会自动使用 tree shaking

-   js使用 ES Module
-   webpack.config.js 中 mode为 production

```js
// 某些情况下会出现 不把样式文件打包进去，这时候需要配置一下package.json中的sideEffects
sideEffects 支持两种写法，一种是 false，一种是数组
1.false 为了告诉 webpack 我这个 npm 包里的所有文件代码都是没有副作用的
2.数组则表示告诉 webpack 我这个 npm 包里指定文件代码是没有副作用的

// package.json
{
    "sideEffects": ["*.css", "*.less"]
}
```



## 5.6 code split （代码分割）

默认情况下，我们打包项目后只会生成一个js文件。code split 将一个庞大的js文件划分成多个小的js文件，从而实现并行加载，让速度更快。 

```js
// 方式1 多入口
const { resolve } = require('path')

module.exports = {
	entry: {
        // 一个入口对应一个bundle，输出一个文件
		index: './src/js/index.js',
		print: './src/js/print.js'
	},
	output: {
		// name 文件名
		filename: "js/[name].[hash:10].js",
		path: resolve(__dirname, 'dist')
	},
	module: {
		rules: [

		]
	},
	plugins: [],
	mode: "production"
}

// 最终生成了2个js文件
```

```js
// 方式2 添加配置项 optimization
1. 可以将node_modules中代码单独打包一个chunk最终输出
2. 自动分析多入口chunk中，有没有公共的文件。如果有会打包成单独一个chunk（多入口时才有效）

// 模拟： 多入口，且index.js 和 pint.js 都引入了 axios 包
const { resolve } = require('path')

module.exports = {
	entry: {
		index: './src/js/index.js',
		print: './src/js/print.js'
	},
	output: {
		// name 文件名
		filename: "js/[name].[hash:10].js",
		path: resolve(__dirname, 'dist')
	},
	module: {
		rules: [

		]
	},
	plugins: [],
	optimization: {
		splitChunks: {
			chunks: "all"
		}
	},
	mode: "production",
}


// 最终生成3个js文件，axios为单独的1个文件。
// 我们在index.js 和 print.js 中都引入了axios，为什么没有生成2个重复的axios文件？
optimization会自动分析多入口chunk中，有没有公共的文件。如果有会打包成单独一个chunk（多入口时才有效）

```



```js
// 方式3 单入口 和 optimization的配合使用

// print.js
export function add(x, y){
	return x + y
}

```

```js
// webpack.config.js
const { resolve } = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
	entry: './src/js/index.js',
	output: {
		// name 文件名
		filename: "js/[name].[hash:10].js",
		path: resolve(__dirname, 'dist')
	},
	module: {
		rules: [

		]
	},
	plugins: [
		new HtmlWebpackPlugin({
			template: "./src/index.html"
		})
	],
	optimization: {
		splitChunks: {
			chunks: "all"
		}
	},
	mode: "production",
}

```

```js
// index.js
import axios from "axios";
// 引入print.js
import add from './print.js'
console.log('index.js')
console.log(axios.get)
console.log(add(1, 2))


1.使用 import add from './print.js' 这种方式引入 print.js，最终打包回输出2个js文件，一个是axios对应的文件,另一个文件包含了index.js 和 print.js的内容

2.如果使用 import() 的方式引入就可以生成3个文件，分别对应 index.js、 print.js 、 axios包

import()返回值是一个Promise对象
如果需要指定print.js打包后的文件名，需要这样写

import (/* webpackChunkName: "print" */ './print').then(({add})=>{
	console.log(add(1, 2))
})
```

### 懒加载

```js
// 我们可以通过在一个回调函数中舒勇 import() 来实现懒加载
// 例如页面有个按钮，我们现在要在用户点击后再去加载对应的js文件

btn.onclick = ()=>{
    import('./print.js').then(res=>{
        // res就是被引入的内容
    })
}
```

### 预加载

```js
// 我们常规引入一个文件时，是并行加载的，当请求多的时候会使网页的加载速度变慢
// 预加载是等其它资源加载完毕后再偷偷加载的，这样既不影响网页的加载速度，又可以提前把要是用的资源加载好

// 这同样依赖于代码分割，使用示例
import(/* webpackPrefetch: true */ './print.js')
```













