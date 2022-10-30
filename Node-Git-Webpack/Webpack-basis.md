**Node中的Path模块**

* dirname：获取文件的父文件夹
* basename：获取文件名
* extname：获取文件扩展名
* **path.resolve**
  * 把一个路径或路径片段的序列解析为一个绝对路径
  * 给定的路径的序列是从右往左被处理的，后面每个 path 被依次解析，直到构造完成**一个绝对路径**
  * 如果没有path传递段，path.resolve()将返回当前工作目录的绝对路径



### webpack.config.js

```js
const path = require("path")
const { VueLoaderPlugin } = require("vue-loader/dist/index")
const { CleanWebpackPlugin } = require("clean-webpack-plugin")
const HtmlWebpackPlugin = require("html-webpack-plugin")
const { DefinePlugin } = require("webpack")

module.exports = {
  mode: "development",//在js中通过process.env.NODE_ENV获取
  
  entry: "./scr/main.js",
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "./build"),
    // clean: true,// 在生成文件之前清空output目录，即CleanWebpackPlugin
  },
  
  resolve: {
    extensions: [".js", ".json", ".vue"],
    alias: {
      utils: path.resolve(__dirname, "./src/utils")
    }
  },
  
  devServer: {
    hot: true,
    port: 8080,
    open: false, //是否打开浏览器
    comporess: false //是否为静态文件开启gzip compression
  }
  
  module: {
    rules: [
      {
        // ***loader	npm i ***lodaer -D
        test: /\.css$/,
        use: [ // 多个loader使用顺序是从后往上
          { loader: "style-loader" }, // 将解析之后的css插入到页面中
          { loader: "css-loader" }, // 只是负责将.css文件进行解析
          {
            loader: "postcss-loader", // CSS的转换和适配，比如自动添加浏览器前缀、css样式的重置
            // 抽取到postcss.config.js中
            // postcss需要有对应的插件才有效果，所以需要配置它的plugin
            /*options: {
              postcssOptions: {
                plugins: [
                  "autoprefixer"
                ]
              }
            }*/
          }
        ]
        // 简写一：只有一个loader
        // loader: "css-loader"
        // 简写二：多个loader不需要其他属性值时
        // use: [ "style-loader", "css-loader" ]
      },
      {
        test: /\.less$/,
        use: [ "style-loader", "css-loader", "less-loader" ]
      },
      {
        // webpack5可以直接使用资源模块类型
        test: /\.(png|jpe?g|svg|gif$)/,
        type: "asset",
        parser: {
          dataUrlCondition: {
            // base64编码or打包
            maxSize: 100 * 1024 // 字节
          }
        },
        generator: {
          filename: "img/[name]_[hash:8][ext]"
        }
      },
      {
        test: /\.js$/,
        use: [ 
          {
            loader: "bable-loader",
            // 抽取到bable.config.js中 
            /*options: {
              presets: ["@bable/loader-env"]
            }*/
          }
        ]
      },
      {
        test: /\.vue$/,
      },
      {}
    ]
  },
  plugins: [
    new VueLoaderPlugin(),
    new CleanWebpackPlugin(),
    new HtmlWebpackPlugin({
      title: "标题",
      template: "./index.html"
    }),
    new DefinePlugin({
      //注册全局变量
      //""当成JS代码执行的
      BASE_URL: "'./'"
    })
  ]
}
```

#### Loader

postcss.config.js

```js
module.exports = {
  // npm i postcss-preset-env -D
  plugins: [
    "postcss-preset-env"
    // 将一些现代的CSS特性，转成大多数浏览器认识的CSS
    // 并且会根据目标浏览器或者运行时环境添加所需的polyfill
    // 已经内置了autoprefixer
  ]
}
```



bable.config.js

常见预设

* env
* react
* TypeScript

```js
module.exports = {
  // npm i @bable/preset-env -D
	presets: [
    "@bable/preset-env"
  ]
}
```



资源模块类型	**asset module type**

* asset/resource：发送一个单独的文件并导出 URL  (file-loader)
  * 打包图片(build中有图片) -> 生成地址 -> 将地址设置到img/bgi中
* asset/inline：导出一个资源的 data URI  (url-loader)
  * 将图片进行base64编码 -> 编码的源码放入打包的js中
  * 可以少发送图片的请求，但是造成js文件大
* asset：在导出一个 data URI 和发送一个单独的文件之间自动选择  (url-loader+配置资源体积)
  * 小图片进行base64编码
  * 大一点的图片单独打包，形成url地址，单独请求这个url图片



resolve模块解析

* 帮助webpack从每个 require/import 语句中，找到需要引入的合适的模块代码

* webpack使用enhanced-resolve解析文件路径
* 模块路径：在 resolve.modules中指定的所有目录检索模块
  *  默认值是 ['node_modules']，所以默认会从node_modules中查找文件
  * 可通过alias进行配置
* 一个文件夹
  * 在文件夹中根据 resolve.mainFiles配置选项中指定的文件顺序查找
  * 默认值是 ['index']，再根据 resolve.extensions来解析扩展名



#### Plugin

Loader是用于特定的模块类型进行转换

Plugin可以用于执行更加广泛的任务，比如打包优化、资源管理、环境变量注入等

* CleanWebpackPlugin：重新打包时自动删除output的目录
* HtmlWebpackPlugin：部署时对应的入口文件index.html
  * 默认根据html-webpack-plugin的源码中，default_index.ejs模板生成的
* DefinePlugin：在编译时创建配置的全局常量



#### Mode

默认是production(node|development|production)

process.env.NODE_ENV

* none：不使用任何优化选项
* development
* production



#### 开启本地服务

在package.json的scripts中配置 "serve": "webpack serve"

编译后不会输出任何文件，而是将bundle文件夹放入到内存中

HMR热模块替换



localhost和0.0.0.0的区别

* localhost：本质上是域名，通常被解析成127.0.0.1
* 127.0.0.1：回环地址(Loop Back Address) 主机自己发出去的包，直接被自己接受
  * 回环地址在网络层直接被获取到了，不会经过数据链路层和物理层
* 0.0.0.0：监听IPV4上所有地址，根据端口找到不同的应用程序



配置区分开发和生产环境

webpack-merge



#### vue create App-name

jsconfig.js

* 给vscode读取，更好代码提示

```js
{
  //...
  "compilerOptions": {
    //...
    "paths": {
      "@/*": [
        "src/*"
      ],
      "utils/*": [
        "src/utils/*"
      ]
    }
    //...
  }
  //...
}
```



vue.config.js

```js
//...
module.exports = defineConfig({
  //...
  // 配置webpack
  configureWebpack: {
    resolve: {
      alias: {
        "utils": "@/utils"
      }
    }
  }
})
```

