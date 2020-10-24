# 引入vue.js

* 后续项目中，我们会使用Vuejs进行开发，而且会以特殊的文件来组织vue的组件。

  * 所以，接下来我们学习如何在webpack环境中集成Vuejs

* 现在，我们希望在项目中使用Vuejs，那么必然需要<u>对其有依赖</u>，所以需要先进行安装

  * 注：因为我们后续是在实际项目中也会使用vue的，所以并不是开发时依赖，所以不加`-dev`

    ```bash
    npm install vue --save
    ```

* 那么，接下来就可以按照我们之前学习的方式来使用Vue了

  > 此前说过三种安装vue的方法，我们用npm安装的方式

# 打包项目-错误信息

* 修改完成后，重新打包，运行程序：

* 运行程序，出现错误，报错如下：
  ![image-20201022085824054](14-webpack%E9%85%8D%E7%BD%AEvue.assets/image-20201022085824054.png)
* 这个错误说的是我们使用的是runtime-only版本的Vue，
* Vue有两种版本的构建方式：
  1. runtime-only：代码中，不可以有任何的template
  2. runtime-compiler：代码中，可以有template，因为有compiler可以用于编译template

* 此处我们修改webpack的配置，添加如下内容即可：
  ![image-20201022090954124](14-webpack%E9%85%8D%E7%BD%AEvue.assets/image-20201022090954124.png)![image-20201022091035637](14-webpack%E9%85%8D%E7%BD%AEvue.assets/image-20201022091035637.png)

# el和emplate区别（一）

* 正常运行之后，我们来考虑另外一个问题：

  * 如果我们希望将data中的数据显示在界面中，就必须修改index.html
  * 如果我们后面自定义了组件，也必须修改index.html来使用组件
  * 但是html模板在之后的开发中，我们并<u>不希望手动的来频繁修改</u>，是否可以做到呢？

* 定义template属性：

  * 在前面的Vue实例中，我们定义了el属性，用于和index.html中的`#app`进行绑定，让Vue实例之后可以管理它其中的内容

  * 这里，我们可以将div中的`{{message}}`删掉，只保留一个基本的id为div的元素

  * 我们可以再定义一个template属性，代码如下：

    ```javascript
    const app = new Vue({
      el: '#app',
      template: `
        <div>
          <h2>{{message}}</h2>
        </div>
      `,
      data: {
        message: 'Hello, World!',
        name: 'coderWhy',
      },
    })
    ```

# el和template区别（二）

* 重新打包，运行程序，显示一样的结果和HTML代码结构
* 那么，el和template模版的关系是什么呢？
  * 在我们之前的学习中，我们知道el用于指定Vue要管理的DOM，可以帮助解析其中的命令、事件监听等等。
  * 而如果Vue实例中同时指定了template，那么template模板的内容会<u>替换掉挂载的对应el的模板</u>。
* 这样做的好处是不需要在未来的开发中再次操作index.html，只需要在template中写入对应的标签即可。
* 但是，书写template模块也比较麻烦
  * 我们可以将template模板中的内容进行抽离。
  * 分成三部分书写：template、script、style，结构会变得非常清晰。

# Vue组件化开发引入

* Vue开发过程中，我们都会采用组件化开发的思想。

  * 那么，在当前项目中，如果我们也想采用组件化的形式进行开发，应该怎么做呢？

* 查看如下代码：

  * 我们也可以将代码抽取到一个js文件中进行导出。

  * ```javascript
    const App = {
      template: `
        <div>
          <h2>{{message}}</h2>
          <button @click="btnClick">按钮</button>
          <h2>{{name}}</h2>
        </div>
      `,
      data () {
        return {yekyi
          message: 'Hello, World!',
          name: 'coderWhy',
        }
      },
      methods: {
        btnClick() {
        }
      },
    }
    const app = new Vue({
      el: '#app',
      template: '<App></App>',
      components: {
        App
      }
    })
    ```

# vue文件封装处理

* 但是一个组件以一个js对象的形式进行组织和使用的时候是非常不方便的。

  * 一方面编写template模块非常的麻烦
  * 另外一方面如果有样式的话，我们该写在哪里会比较合适呢？

* 现在，我们以一种全新的方式来组织一个vue的组件

* 到那时，这个时候这个文件可以被正确的加载吗？

  * 必然不可以，这种特殊的文件以及特殊的格式，必须有人帮助我们处理。
  * 谁来处理呢？`vue-loader`以及`vue-template-compiler`。

* 通过npm进行安装

  ```BASH
  npm install vue-loader@13.0.0 vue-template-compiler@2.6.12 --save-dev
  ```

* 修改webpack.config.js的配置文件：

  ```javascript
  {
      test: /\.vue$/,
      use: ['vue-loader']
  }
  ```

## 导入文件时写不写后缀的问题

* 如果在引入的时候不想写后缀

  ```javascript
  <script>
    import Cpn from './Cpn.vue' // here!
    export default {
      name: "App",
      components: {
        'Cpn': Cpn,
      },
  ...
  ```

* 可以在webpack.config.js中resolve中加入`extensions`，如下所示

  ```javascript
  resolve: {
    extensions: ['.js', '.css', '.vue'], // here
    alias: {
        'vue$': 'vue/dist/vue.esm.js'
    },
  },
  ```

* 这样在导入的过程中，就不需要加后缀了

  ```javascript
  <script>
    import Cpn from './Cpn' // here!
  ...
  ```

# 认识plugin

* plugin是插件，通常用于对某个现有的架构进行扩展。
  * webpack中的插件，就是对webpack现有功能的各种扩展，比如打包优化，文件压缩等等。
* loader和plugin的区别
  * loader主要用于<u>转换某些类型</u>的模块，它是一个转换器。
  * plugin是插件，它是对webpack本身的扩展，是一个<u>扩展器</u>。

* plugin的使用过程：
  1. 通过npm安装需要使用的plugins（某些webpack已经内置的插件不需要安装）
  2. 在`webpack.config.js`中的plugins中配置插件。
* 接下来，看看通过哪些插件对现有的webpack打包过程<u>进行扩容</u>，让我们的webpack变得更加好用。

## 添加版权的plugin

* 首先介绍一个最简单的插件，为打包的文件添加<u>版权声明</u>。

  * 该插件名字叫`BannerPlugin`，属于webpack自带的插件。

* 按照下面的方式来修改`webpack.config.js`文件：

  ```javascript
  const path = require('path');
  const webpack = require('webpack')
  module.exports = {
    plugins: [
      new webpack.BannerPlugin('最终版权归DongyangYu所有')
    ],
    ...
  }
  ```

* 重新打包程序：查看bundle.js文件的头部，看到如下信息
  ![image-20201022142613842](14-webpack%E9%85%8D%E7%BD%AEvue.assets/image-20201022142613842.png)



## 打包html的plugin

* 目前，我们的index.html文件是存放在项目的根目录下的。

  * 在发布项目时，发布的是dist文件夹，这个时候就可以使用HtmlWebpackPlugin插件

* HtmlWebpackPlugin插件可以为我们做这些事情：

  * 自动生成一个index.html文件（可以指定模板来生成）

  * 将打包的js文件，<u>自动通过script标签插入到body中</u>

  * 安装HtmlWebpackPlugin插件

    ```bash
    npm install html-webpack-plugin --save-dev
    ```

* 使用插件，修改webpack.config.js文件中plugins部分的内容如下：

  ```javascript
  const htmlWebpackPlugin = require('html-webpack-plugin')
  module.exports = {
    plugins: [
        ...
        new htmlWebpackPlugin({
        template: 'index.html'
      }),
    ],
    output: {
      path: path.resolve(__dirname, 'dist'), //动态获取路径, resolve是拼接
      filename: 'bundle.js',
    // publicPath: 'dist/', // 需要删除，因为生成后bundle.js和html在同一目录下
    },
  ...
  }
  ```
  
* 这里的template表示根据什么模板来生成index.html
  * 另外，我们需要**<u>删除</u>**之前在output中添加的pluginPath属性
  * 否则插入的script标签中的src可能会有问题
  
    
  

## js压缩的plugin（丑化）

> 在开发阶段不建议使用丑化插件，因为会对调试造成阻碍。

* 在项目发布之前，我们必然需要对js等文件进行压缩处理

  * 这里，我们就对打包的js文件进行压缩

  * 我们使用一个第三方的插件`uglifyjs-webpack-plugin`，并且版本号指定1.1.1，和CLI2保持一致

    ```bash
    npm install uglifyjs-webpack-plugin@1.1.1 --save-dev
    ```

* 修改webpack.config.js文件，使用插件：

  ```javascript
  const UglifyjsWebpackPlugin = require('uglifyjs-webpack-plugin')
  
  module.exports = {
    plugins: [
      ...
      new UglifyjsWebpackPlugin(),
    ],
  }
  ```

* 再查看打包后的bundle.js文件，发现已经被压缩过了（丑化以达到压缩的目的）。
  ![image-20201022153027295](14-webpack%E9%85%8D%E7%BD%AEvue.assets/image-20201022153027295.png)

# 搭建本地服务器

* webpack提供了一个可选的本地开发服务器，这个本地服务器基于node.js搭建，内部使用express框架，可以实现我们想要的，即让浏览器自动刷新显示我们修改后的结果。

* 不过它是一个单独的模块，在webpack中使用之前需要先安装它

  ```bash
  npm install --save-dev webpack-dev-server@2.9.1
  ```

* devserver也是作为webpack中的一个选项，选项本身可以设置如下属性：

  * `contentBase`：为哪一个文件夹提供本地服务，默认是根文件夹，我们这里要填写`./dist`
  * `port`：端口号
  * `inline`：页面是否实时刷新
  * `historyApiFallback`：在SPA页面中，依赖HTML5的history模式

* webpack.config.js文件配置如下：

  ```javascript
  module.exports = {
    devServer: {
      contentBase: './dist',
      inline: true, // 是否实时监听
    },
    ...
  }
  ```

* 我们可以再配置另外一个scripts：

  * `--open`参数表示直接打开浏览器
    ![image-20201022155218995](14-webpack%E9%85%8D%E7%BD%AEvue.assets/image-20201022155218995.png)

# webpack配置文件的分离

* 主目录下创建`build`文件夹，用于<u>存放所有配置文件</u>；
  ![image-20201022161136868](14-webpack%E9%85%8D%E7%BD%AEvue.assets/image-20201022161136868.png)

* 安装

  ```BASH
  npm install webpack-merge@4.1.5 --save-dev
  ```

* 将之前webpack.config.js的代码全部拷贝到base.config.js中，同时删除webpack.config.js文件，接着改写prod和dev两个配置文件，目的是将它们分离

  * 在prod.config.js中写入

  ```javascript
  const UglifyjsWebpackPlugin = require('uglifyjs-webpack-plugin')
  const WebpackMerge = require('webpack-merge')
  const baseConfig = require('./base.config.js')
  
  module.exports = WebpackMerge(baseConfig, {
    plugins: [
      new UglifyjsWebpackPlugin(), //开发阶段不建议用丑化插件
  
    ],
  })
  ```

  * 在dev.config.js中写入

  ```JavaScript
  const WebpackMerge = require('webpack-merge')
  const baseConfig = require('./base.config.js')
  
  module.exports = WebpackMerge(baseConfig, {
    devServer: {
      contentBase: './dist',
      inline: true, // 是否实时监听
    },
  })
  ```

* 但此时打包运行命令`npm run build`会报错
  ![image-20201022162202413](14-webpack%E9%85%8D%E7%BD%AEvue.assets/image-20201022162202413.png)

  * 原因是找不到配置文件，我们需要去`package.json`中改写`scripts`脚本，分别加上`–config 对应文件名路径`

    ```JavaScript
    "scripts": {
      "build": "webpack --config ./build/prod.config.js",
      "dev": "webpack-dev-server --open --config ./build/dev.config.js"
    },
    ```

## 打包成功，但路径不对

![image-20201022162541793](14-webpack%E9%85%8D%E7%BD%AEvue.assets/image-20201022162541793.png)

* 看上图，打包成功后，文件都跑到了build文件夹下，为什么呢？
  * ![image-20201022162700404](14-webpack%E9%85%8D%E7%BD%AEvue.assets/image-20201022162700404.png)
  * 原因是output的路径是当前文件夹下的`dist文件夹`，因此需要改成`../dist`。再次打包，成功。