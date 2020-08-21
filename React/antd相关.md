## 1.antd按需加载（不解封webpack）

```shell
yarn add react-app-rewired customize-cra babel-plugin-import antd
```

```js
// 在项目根目录下创建 config-overrides.js
const {override, fixBabelImports} = require("customize-cra");

module.exports = override(
    fixBabelImports('import', {
        libraryName: 'antd',
        libraryDirectory: 'es',
        style: 'css',
    }),
);
```



## 2.使用less和antd按需加载

```shell
npm i less less-loader@4.0.1 babel-plugin-import antd
```

```json
// 运行 npm run eject
// 在webpack.config.js中将对应的sass配置改为less
// 在jsx的loader的plugins数组中添加
["import", {"libraryName": "antd", "style": "css"}]

```



