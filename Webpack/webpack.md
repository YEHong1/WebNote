# 第 1 章：webpack 简介



## 1.1 webpack 是什么

-   webpack 是一种前端资源构建工具，一个静态模块打包器(modulebundler)。
-    在 webpack 看来, 前端的所有资源文件(js/json/css/img/less/...)都会作为模块处理。 
-   它将根据模块的依赖关系进行静态分析，打包生成对应的静态资源(bundle)。



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
    npm init
    ```

    

2.  ### 下载并安装 webpack 

    ```
    npm i webpack webpack-cli -g
    ```

    



## 2.2 编译打包应用

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
//能将es6模块化语法转换为浏览器能识别的语法
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
                exclude: /\.(css|js|html|less)$/,
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
                test: /\.css$/,
                use: [
                    MiniCssExtractPlugin.loader,
                    'css-loader',
                    /*css的兼容处理  ==>    postcss-loader、 postcss-preset-env (插件)
                    * postcss-preset-env 帮 postcss找到package.json中browserslist里面的配置，通过配置加载					指定的css兼容性样式
                    * "browserslist": {
                          // 开发环境 --> 设置node环境变量：process.env.NODE_ENV = 'development'
                          "development": [
                            //兼容最近版本的浏览器
                            "last 1 chrome version",
                            "last 1 firefox version",
                            "last 1 safari version"
                          ],
                          // 生产环境：默认是看生产环境
                          "production": [
                            ">0.2%",
                            "not dead",
                            "not op_mini all"
                          ]
                        }
                    * */

                    //使用loader的默认配置
                    // 'postcss-loader'

                    //修改loader的配置
                    {
                        loader: 'postcss-loader',
                        options: {
                            ident: 'postcss',
                            plugins: () => [
                                //postcss的插件
                                require('postcss-preset-env')()
                            ]
                        }
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
            //对输出的css文件进行重命名
            filename: 'css/built.css'
        })
    ],
    mode: "development",
};
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
                        loader: 'postcss-loader',
                        options: {
                            ident: 'postcss',
                            plugins: () => [
                                //postcss的插件
                                require('postcss-preset-env')()
                            ]
                        }
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
            //对输出的css文件进行重命名
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
>   eslint-config-airbnb-base 不包含react语法检查，可以取npm 搜索 airbnb
>
>    npm install --save-dev eslint-loader eslint eslint-config-airbnb-base eslint-plugin-import 

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
            /*语法检查： eslint-loader eslint
            注意：只检查自己写的源代码，第三方的库是不用检查的
            设置检查规则：
            package.json 中 eslintConfig 中设置~
            "eslintConfig": {
                "extends": "airbnb-base"
            }
            airbnb --> eslint-config-airbnb-base eslint-plugin-import eslint
            */
            {
                test: /\.js$/,
                // 不检查node_module目录下的js
                exclude: /node_modules/,
                loader: 'eslint-loader',
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

```js
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





































