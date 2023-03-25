小知识

- [contenhash] [chunkhash] [hash]



mode：'none' | 'development' | 'production'(默认值)
![image-20230321230801266](img/image-20230321230801266.png)



devtool
 -- sourcemap：使浏览器可以重构原始源并在调试器中显示重建的原始源

```js
/* webpack.config.js */
module.exports = {
  // 常见的值:
  // 1.false
  // 2.none => production模式下的默认值
  // 3.eval => development模式下的默认值 报错结果没有那么准确
  // 4.source-map => production

  // 不常见的值: 
  // 1.eval-source-map: 添加到eval函数的后面
  // 2.inline-source-map: 添加到文件的后面
  // 3.cheap-source-map(dev环境): 低开销, 更加高效--没有生成列映射
  // 4.cheap-module-source-map: 和cheap-source-map相似, 但是对来自loader的source-map处理的更好
  	//		比如空行等信息会保留，源代码中正确的行数
  // 5.hidden-source-map(prod): 会生成sourcemap文件, 但是不会对source-map文件进行引用。
  	//		需要手动添加注释# sourceMappingURLxxx
  // 6.nosources-source-map:生成的sourcemap只有错误信息提示，没有源代码文件
  devtool: 'srouce-map'
}
```

**浏览器会根据注释，查找相应的source-map，并且根据source-map还原代码，方便进行调试**

![image-20230321202530904](./img/image-20230321202530904.png)



-  开发阶段：推荐使用 source-map或者cheap-module-source-map
  - 这分别是vue和react使用的值，可以获取调试信息，方便快速开发
- 测试阶段：推荐使用 source-map或者cheap-module-source-map
  - 测试阶段也希望在浏览器下看到正确的错误提示
- 发布阶段：false、缺省值（不写）



## 配置分离

```json
/* package.json */ 
// --config指定使用配置文件目录，--env指定环境
{
  "scripts": {
    "build": "webpack --config ./config/comm.config.js --env production",
    "server": "webpack serve --config ./config/comm.config.js --env development"
  }
}
```

npm i webpack-merge -D

```js
/* config/comm.config.js */
const path = require('path')
const { merge } = require('webpack-merge')

const devConfig = require('./dev.config')
const prodConfig = require('./prod.config')

const getCommConfig = function(isProdution) {
  return {
    output: {
      path: path.resolve(__dirname, '../build'),
    },
    module: {
      rules: [
        {
          test: /\.css$/,
          use: [
            // // 'style-loader', //开发阶段
            // MiniCssExtractPlugin.loader, // 生产阶段
            isProdution ? MiniCssExtractPlugin.loader: 'style-loader',
            'css-loader'
          ]
        }
      ]
    }
  }  
}

// webpack允许导出一个函数
module.exports = function(env) {
  const isProduction = env.production
  const mergeConfig = isProduction ? prodConfig : devConfig
  return merge(getCommConfig(isProduction), mergeConfig)
}
```



```js
/* config/dev.config.js */
module.exports = {}
```



```js
/* config/prod.config.js */
module.exports = {}
```



## 打包时间分析

npm i speed-measure-webpack-plugin -D

```js
/* webpack.config.js */
const SpeedMeasurePlugin = require('speed-measure-webpack-plugin')

const smp = new SpeedMeasurePlugin() // 使用 smp.wrap 包裹我们导出的webpack配置
```

## 打包文件分析

方案一：

```json
/* package.json */ 
// 把打包信息存到stats.json文件中
{
  "scripts": {
    "build": "webpack --config ./config/comm.config.js --env production --profile --json=stats.json",
  }
}
```

启动analyse-master项目
把stats.json上传后这个项目会对json文件进行分析



方案二：

npm i webpack-bundle-analyzer -D

```js
/* webpack.config.js */
const { BundleAnalyzerPlugin } = require('webpack-bundle-analyzer')

  plugins: [
    // 对打包后的结果进行分析
    new BundleAnalyzerPlugin()
  ]
```





# Babel

Babel是一个工具链，主要用于旧浏览器或者缓解中将ECMAScript 2015+代码转换为向后兼容版本的JavaScript（包括：语法转换、源代码转换、Polyfill实现目标环境缺少的功能等）
Babel本身可以作为**一个独立的工具**（和postcss一样），不和webpack等构建工具配置来单独使用
Babel底层原理：
可以将Babel看成就是一个编译器----将我们的源代码，转换成浏览器可以直接识别的另外一段源代码

- 解析阶段
- 转换阶段： AST树 -> 应用plugins -> 新的AST树
- 生成阶段
-  js编写的优秀编译器案例：https://github.com/jamiebuilds/the-super-tiny-compiler

![image-20230321232230495](img/image-20230321232230495.png)





npm i @babel/core @babel/cli -D
npm i @babel/preset-env
npx babel ./src --out-dir ./build --presets=@babel/preset-env
预设会跟 .browserslistrc 中的设置有关
常见预设

- env

* react
* TypeScript

npm i babel-loader -D

```js
/* webpack.config.js */
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        use: {
          loader: 'babel-loader', // 相当于webpack和Babel间的桥梁
        },
      },
    ],
  },
}

```



```js
/* babel.config.js */
module.exports = {
  presets: [
    ["@babel/preset-env", {
      // 在开发中针对babel的浏览器兼容查询使用browserslist工具, 而不是设置target
      // 因为browserslist工具, 可以在多个前端工具之间进行共享浏览器兼容性(postcss/babel)
      // targets: ">5%"
    }]
  ]
}
```

- **browserslist**是一个在不同的前端工具之间，共享目标浏览器和Node.js版本的配置
  - 条件查询使用的是**caniuse-lite**的工具，这个工具的数据来自于caniuse的网站上



babel提供了**两种配置文件的编写**：

- babel.config.json（或者.js，.cjs，.mjs）文件
  - （babel7）可以直接作用于Monorepos项目的子包，更加推荐
    - Monorepos：把所有项目的代码统一维护在单一的代码版本库中
      这些项目可能是相关的，但通常在逻辑上是独立的
- .babelrc.json（或者.babelrc，.js，.cjs，.mjs）文件
  - 对于配置Monorepos项目是比较麻烦的



### Stage-X

**TC39 遵循的原则是：分阶段加入不同的语言特性，新流程涉及四个不同的 Stage**

**Stage 0：**strawman（稻草人），任何尚未提交作为正式提案的讨论、想法变更或者补充都被认为是第 0 阶段的"稻草人"
**Stage 1：**proposal（提议），提案已经被正式化，并期望解决此问题，还需要观察与其他提案的相互影响
**Stage 2：**draft（草稿），Stage 2 的提案应提供规范初稿、草稿。此时，语言的实现者开始观察 runtime 的具体实现是否合理
**Stage 3：**candidate（候补），Stage 3 提案是建议的候选提案。在这个高级阶段，规范的编辑人员和评审人员必须在最终规范上签字。Stage 3 的提案不会有太大的改变，在对外发布之前只是修正一些问题
**Stage 4：**finished（完成），进入 Stage 4 的提案将包含在 ECMAScript 的下一个修订版中



在babel7之前（比如babel6中），我们会经常看到这种设置方式：

```js
preset: ["stage-0"]
```

它表达的含义是使用对应的 babel-preset-stage-x 预设
但是从babel7开始，已经不建议使用了，建议使用preset-env来设置



## polyfill

某些浏览器不认识一些新的特性，需要打上补丁
babel7.4.0之前，可以使用 @babel/polyfill的包，但是该包现在已经不推荐使用了，使用下列方案

npm i core-js regenerator-runtime -D

```js
/* babel.config.js */
module.exports = {
  presets: [
    ["@babel/preset-env", {
      corejs: 3,
      // 'false': 不使用polyfill进行填充,也不需要设置corejs
      // 'usage': 根据代码所用的API自动引入相关polyfill
      useBuiltIns: "entry", 
      // 假如依赖的某一个库本身使用了某些polyfill的特性就需要使用 'entry'
      // 'entry': 代码中手动import 'core-js/stable' 'regenerator-runtime/runtime'
      // 会根据 browserslist 目标导入所有的polyfill，但是对应的包也会变大
    }],
  ]
}
```



### React的jsx支持

npm i react react-dom => 编写jsx代码
npm i html-webpack-plugin -D => 打包index.html

```js
/* webpack.config.js */
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  resolve: {
    extensions: ['.js', '.json', '.wasm', '.jsx']
  },
  module: {
    rules: [	
      {
        test: /\.jsx?$/,
        use: { loader: "babel-loader" }
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({ template: './index.html' })
  ]
}
```

npm i @babel/preset-react -D

```js
/* babel.config.js */
module.exports = {
  presets: [
    ["@babel/preset-env", {}],
    ["@babel/preset-react"]
  ]
}
```

#### TypeScript

npm i typescript -D
方案一：npm i ts-loader -D   (typescript compiler，不包含poltfill)

```js
/* webpack.config.js */
module.exports = {
  resolve: {
    extensions: ['.js', '.json', '.wasm', '.jsx', 'ts']
  },
  module: {
    rules: [
      {
        test: /\.ts$/,
        use: "ts-loader"
      }
    ]
  }
}
```

tsc --init => 生成tsconfig.json



**方案二**：  使用babel-loader  (可以实现polyfill，但是**不会进行类型检测**(需要tsc))
npm i @babel/preset-typescript -D
{
        test: /\.ts$/,
        use: "babel-loader"
}

```js
/* babel.config.js */
module.exports = {
  presets: [
    ["@babel/preset-env", {}],
    ["@babel/preset-typescript", {
      corejs: 3,
      useBuiltIns: "entry",
    }]
  ]
}
```



##### TS最佳实践

scripts中添加脚本命令 "**tsc --noEmit --watch**"

- --noEmit 进行类型检测，但不输出文件
- --watch 在终端实时监听





# 本地服务器

效率高----bundle文件是**保留在内存中**

npm i webpack-dev-server -D
scripts中增加脚本命令 "webpack server"

- webpack-dev-server使用了一个库叫memfs（memory-fs webpack自己写的）

```js
/* webpack.config.js */
module.exports = {
  devServer: {
    open: false, //是否打开浏览器
    comporess: false, //是否为静态文件开启gzip compression  在服务端压缩
    static: ['public', 'content'], // 静态文件夹,默认有public
    historyApiFallback: true,
  }
}
```

**historyApiFallback**：主要的作用是解决SPA页面在路由跳转之后，进行页面刷新时，返回404的错误

- 设置为true，那么在刷新时，返回404错误时，会自动返回 index.html 的内容
- vue等脚手架已经设置好了



## Proxy代理

本质是使用了http-proxy-middleware软件包

```js
/* webpack.config.js */
module.exports = {
  devServer: {
    port: 8888,
    proxy: {
      '/api': {
        target: 'htpp://localhost:9000',
        pathRewrite: {
          '^/api': ''
        },
        changeOrigin: true
      }
    }
  }
}
```

- axios.get('/api/users/list') ->请求的是webpack开启的服务器 'http://localhost:8888/api/users/list'
  变为axios.get('http://localhost:9000/api/users/list')
- pathRewrite后变为axios.get('http://localhost:9000/users/list')
  - 这样请求不会产生跨域（因为资源就部署webpack的服务器上）
    - 等于静态资源和API指向同一个服务器
- **changeOrigin**：假设不开启，target服务器拿到的headers->host还是 **localhost:8888**
  - 开启后，target服务器拿到的headers->host变为了 **localhost:9000**





# 性能优化方案

结果进行优化：
1.分包处理 react/vue路由懒加载
2.代码丑化
3.删除无用代码(Tree Shaking)
4.CDN服务器

打包过程优化：
1.exclue/cache



## 分包处理

不进行分包的话，bundle.js中包含了1.自己编写的代码 2.第三方库的代码 3.webpack为支持模块化，运行时的代码

- 不方便进行管理
- **首包太大，首屏渲染速度慢，长时间空白页面**（路由懒加载）

首屏渲染速度：①分包处理后，可以prefetch ②SSR（加快首屏渲染速度，增加SEO优化）



### 入口处理

多入口起点

```js
/* webpack.config.js */
module.exports = {
  entry: {
    // 配置多入口
    /*index: './src/index.js',
    main: './src/main.js' */
    
    // 指定需要共享的
    index: {
      import: './src/index.js',
      dependOn: 'shared1'
    },
    main: {
      import: './src/main.js',
      dependOn: 'shared2'
    },
    shared1: ['axios'],
    shared2: ['dayjs']
  },
  output: {
    path: path.resolve(__dirname, "./build"),
    // [name] 占位符
    filename: '[name]-bundle.js',
  }
}
```

- **shared**



### 防止重复

使用Entry Dependencies或者SplitChunksPlugin去重和分离代码

- Entry Dependencies 就是上方的shared

- splitChunks    它底层是使用SplitChunksPlugin来实现的

```js
/* webpack.config.js */
module.exports = {
  optimization: {
    splitChunks: {
      // 默认async: 默认异步导入的才分包
      chunks: "all",
      minSize: 10, // 默认是大于20kb才会拆包
      // 自己对需要进行拆包的内容进行分包
      cacheGroups: {
        vendors: {
          // windos上面路径 / \   mac上面路径 /
          test: /[\\/]node_modules[\\/]/,
          filename: "[id]_vendor.js"
        },
        utils: {
          test: /utils/,
          filename: "[id]_utils.js"
        }
      }
    }
  }
}
```



解决注释的单独提取

- minimizer--webpack在进行分包时，有对包中的注释进行单独提取

```js
/* webpack.config.js */
const TerserPlugin = require('terser-webpack-plugin')  // webpack5本身自动安装了

  optimization: {
    // 代码优化: 默认情况是没压缩的，使用了TerserPlugin => 让代码更加简单 => Terser
    minimizer: [
      // js代码简化
      new TerserPLugin({
        // 默认情况(true)下，webpack在进行分包时，有对包中的注释进行单独提取
        extractComments: false
      })
    ]
  }
```



### 动态导入

通过模块的内联函数调用来分离代码

import()函数导入--在webpack中，通过动态导入获取到一个对象。真正导出的内容，在该对象的default属性中



路由的实现  location.hash / history.push
监听变化，再加载（懒加载）



动态导入通常是一定会打包成独立的文件的

```js
/* main.js */
const btn1 = document.createElement('button')
const btn2 = document.createElement('button')
btn1.textContent = '关于'
btn2.textContent = '分类'
document.body.append(btn1)
document.body.append(btn2)

btn1.onclick = function() {
  import(
    /* webpackChunkName: "about" */
    './router/about').then(res => {
    res.about()
    res.default()
  })
}
btn2.onclick = function() {
  import(
    /* webpackChunkName: "category" */
    './router/category')
}
```
魔法注释

- /* webpackChunkName */ -->给[name]指定名称
```js
/* webpack.config.js */
module.exports = {
  output: {
    path: path.resolve(__dirname, "./build"),
    filename: '[name]-bundle.js',
    // 单独针对分包文件的命名
    chunkFilename: '[name]_chunk.js'
  }
}
```



Prefetch和Preload

- prefetch chunk 会在父 chunk 加载结束后开始加载。preload chunk 会在父 chunk 加载时，以并行方式开始加载
  prefetch chunk 在浏览器闲置时下载。preload chunk 具有中等优先级，并立即下载
  prefetch chunk 会用于未来的某个时刻。preload chunk 会在父 chunk 中立即请求，用于当下时刻

  ```js
  btn1.onclick = function() {
    import(
      /* webpackChunkName: "about" */
      /* webpackPrefetch: true */
      './router/about').then(res => {
      res.about()
      res.default()
    })
  }
  
  btn2.onclick = function() {
    import(
      /* webpackChunkName: "category" */
      /* webpackPrefetch: true */
      './router/category')
  }
  ```



chunkIds：告知webpack模块的id采用什么算法生成

```js
  optimization: {
    // 设置生成的chunkId的算法
    // development: named
    // production: deterministic(确定性)，在不同编译中不变的短数字id  包没变化时不会重新打包  -> 推荐使用
    // webpack4中使用: natural 自然数，名称容易变化，不利于浏览器缓存
    chunkIds: 'deterministic',
  }
```



runtimeChunk--在运行环境中，对模块进行解析、加载、模块信息相关的代码--有利于浏览器缓存的策略
- 比如修改了业务代码（main），那么runtime和component、bar的chunk是不需要重新加载的
  比如修改了component、bar的代码，那么main中的代码是不需要重新加载的；

```js
  optimization: {
		runtimeChunk: {
      name: "runtime"
    }
  }
```





## CDN

**内容分发网络**：通过相互连接的网络系统，利用最靠近每个用户的服务器将数据传给用户

- 第三方资源放到CDN服务器上
  2.在html模块中，我们需要自己加入对应的CDN服务器地址：index.html中加上
   \<script src="https://cdn.bootcdn.net/ajax/libs/axios/1.2.0/axios.min.js"></script\>
  \<script src="https://cdn.bootcdn.net/ajax/libs/react/18.2.0/umd/react.production.min.js"></script\>

```js
/* webpack.config.js */
module.exports = {
  output: {
    // 方案一: 所有的资源都放在CDN
    // 打包的所有静态资源放到服务器上
    // 会在打包时自动添加上CDN地址
    // publicPath: 'CDN地址'
  },
  // 方案二: 使用第三方资源部署的CDN
  // 1.排除某些包不需要进行打包
  externals: {
    react: "React",
    // key属性名: 排除的框架的名称  import from 'react'/'axios'
    // value值: 从CDN地址请求下来的js中提供的对应的名称
    axios: "axios"
  }
}
```



## 提取CSS文件

npm i mini-css-extract-plugin -D
帮助我们将css提取到一个独立的css文件中，该插件需要在webpack4+才可以使用

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin')

/* webpack.config.js */
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          // 'style-loader', 开发阶段 -> 将css以style内联的方式插入html
          MiniCssExtractPlugin.loader, // 生产阶段 -> 提取到单独文件，通过link元素引入
          'css-loader'
        ]
      }
    ]
  },
  plugins: [
    // 完成css的提取
    new MiniCssExtractPlugin({
      filename: 'css/[name].css',  //   css/ 放到单独文件夹中
      chunkFilename: 'css/[name]_chunk.css' // 对动态导入的css分包起名
    })
  ]
}
```



## Terser

Terser是一个JavaScript的Parser（解释）、Mangler（绞肉机）/Compressor（压缩机）的工具集
可以压缩、丑化代码-----从 uglify-es fork 过来的，并且保留它原来的大部分API以及适配 uglify-es和uglify-js@3等

https://github.com/terser/terser#compress-options
https://github.com/terser/terser#mangle-options

npm i terser -D
npm i css-minimizer-webpack-plugin -D  (这个插件是使用cssnano工具进行优化css的)

```js
/* webpack.config.js */
const TerserPlugin = require('terser-webpack-plugin')
const CSSMinimizerPlugin = require('css-minimizer-webpack-plugin')

  optimization: {
    minimizer: true,
    // 代码优化: 默认情况是没压缩的，使用了TerserPlugin => 让代码更加简单 => Terser
    minimizer: [
      // js代码压缩简化
      new TerserPlugin({
        extractComments: false, // 默认true，表示会将注释抽取到一个单独的文件中,不希望保留这个注释时，可以设置为false
        terserOptions: {}, // 定制化
        parallel: true// 使用多进程并发运行提高构建速度
      }),
      // css代码压缩简化 通常是去除无用的空格等
      new CSSMinimizerPlugin({
        // parallel: true
      })
    ]
  }
```

terserOptions：设置terser相关的配置
	compress：设置压缩相关的选项
	mangle：设置丑化相关的选项，可以直接设置为true
	toplevel：顶层变量是否进行转换
	keep_classnames：保留类的名称
	keep_fnames：保留函数的名称



## Tree Shaking

用于消除未调用的代码--纯函数无副作用，可以放心消除，这也是为什么要求我们在进行函数式编程时，尽量使用纯函数的原因之一
对JavaScript进行Tree Shaking是源自打包工具rollup

- Tree Shaking依赖于ES Module的静态语法分析（不执行任何的代码，可以明确知道模块的依赖关系）
- 在webpack4正式扩展了这个能力
- webpack5中，也提供了对部分CommonJS的Tree Shaking的支持--https://github.com/webpack/changelog-v5#commonjs-tree-shaking



### js-Tree Shaking

- 在optimization中配置usedExports为true，来帮助Terser进行优化
- 在package.json中配置sideEffects，直接对模块进行优化



#### usedExports

通过标记某些函数是否被使用，之后通过Terser来进行优化的
devlopment下观察（production模式下，默认设置会有很大影响，不易观察）

```js
/* webpack.config.js */
module.exports = {
  optimization: {
    // 导入模块时分析哪些函数有被使用，哪些没被使用
    usedExports: true, // production模式下自动开启
    // 再搭配TerserPlugin
  }
}
```



#### sideEffects

跳过整个模块/文件，直接查看该文件是否有副作用
当我们 import 'xxx'时，即使没有使用这个模块中的任何内容，webpack打包时也不会把这个模块整个删除

```js
/* package.json */
{
  // "sideEffects": false // 告知webpack所有导入文件都没有副作用
  "sideEffects": [ // 数组的话告诉webpack哪些文件存在副作用
    "./src/utls/math.js",
    "*.css"
  ]
}
```



### css-Tree Shaking

删除元素中并没有使用相关的 id/css选择器及其样式
它是对**打包后的css**进行tree shaking操作(也可以对less文件进行处理)

npm i purgecss-webpack-plugin -D
npm i glob -D  (glob需要安装7.*的版本)



和 **MiniCssExtractPlugin** 结合使用

```js
/* webpack.config.js */
const path = require('path')
const glob = require('glob') // node中经常使用的模块
const { PurgeCssPlugin } = require('purgecss-webpack-plugin')

  plugins: [
    new PurgeCssPlugin({
      paths: glob.sync(`${path.resolve(__dirname, './src')}/**/*`, { nodir: true }), // 立即获取所有的文件路径
      // 默认情况下，Purgecss会将html标签的样式移除掉，如果希望保留，可以添加一个safelist的属性
      safelist: function() { 
        return {
          standard: ['html']
        } 
      }
    })
  ]
```



## Scope Hoisting

对作用域进行提升，并且让webpack打包后的代码更小、运行更快
Scope Hoisting可以将函数合并到一个模块中来运行
多个模块的东西尽量放到一个模块中，代码运行速度更快

```js
/* webpack.config.js */
	plugins:[
    new webpack.optimize.ModuleConcatenationPlugin() // production模式下，默认启用
  ]
```



## HTTP压缩

1.HTTP数据在服务器发送前就已经被压缩了（在webpack中完成）
2.兼容的浏览器在向服务器发送请求时，会告知服务器自己支持哪些压缩格式
3.服务器在浏览器支持的压缩格式下，直接返回对应的压缩后的文件，并且在响应头中告知浏览器

npm i compression-webpack-plugin -D

```js
/* webpack.config.js */
const CompressionPlugin = require("compression-webpack-plugin")

	plugins:[
    // 对打包后的文件(js/css)进行压缩
    new CompressionPlugin({
      test: /\.(js|css)$/,
      algorithm: 'gzip'
    })
  ]
```



## HTML文件压缩

```js
	plugins:[
    // 对打包后的文件(js/css)进行压缩
    new HtmlWebpackPlugin({
      template: './index.html',
      cache: true, // 只有当文件改变时，才会生成新的文件（默认值true）
      // 默认会使用一个插件html-minifier-terser
      minify: isProdution? { 
        removeComments: true,  // 移除注释
        removeEmptyAttributes: true,
        removeRedundantAttributes: true,  // 移除默认属性
        collapseWhitespace: true,  // 折叠空白字符
        minifyCSS: true,  // 压缩内联的CSS
        minifyJS: {  // 压缩html文件中<scipt>标签中的JavaScript
          mangle: {
            toplevel: true
          }
        }
      } : false
    })
  ]
```









