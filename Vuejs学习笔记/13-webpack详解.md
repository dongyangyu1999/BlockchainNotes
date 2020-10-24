# 什么是Webpack

* 官方解释：本质上讲，webpack是一个现代的JavaScript应用的静态**模块打包**工具。
* 我们从两个点来解释上面这句话：**模块**和**打包**

## 前端模块化

* 在前面学习中，我们已经用了大量的篇幅解释了为什么前端需要模块化。
* 而且我们也提到了目前使用前端模块化的一些方案：AMD、CMD、CommonJS、ES6。
* 在ES6之前，我们要想进行模块化开发，就必须借助于其它的工具，让我们可以进行模块化开发。
* 并且在通过模块化开发完成了项目后，还需要处理模块间的各种依赖，并且将其进行整合打包。
* 而webpack其中一个核心就是让我们可以进行模块化开发，并且会<u>帮助我们处理模块间的依赖关系</u>。
* 而且不仅仅是JavaScript文件，我们的CSS、图片、json文件等等在webpack中都可以被当做模块来使用。
* 这就是webpack中模块化模块化的概念。

## 打包

* 打包就是将webpack中的各种资源模块进行打包和并成一个或多个包（bundle）。
* 并且在打包的过程中，还可以对资源进行处理，比如压缩图片，将scss转成css，将ES6语法转成ES5语法，将TypeScript转成JavaScript等等操作。

### 与grunt/gulp的对比

> 但打包的操作似乎grunt/gulp也可以帮助我们完成，它们有什么不同呢？

* grunt/gulp的核心是Task
  * 我们可以配置一系列的task，并且定义task要处理的事务（例如ES6，ts转化，图片压缩，scss转成css）
  * 之后让grunt/gulp来依次执行这些task，而且让整个流程自动化。
  * 所以grunt/gulp也被称为<u>前端自动化任务管理工具</u>。

* 我们来看一个gulp的task
  * 下面的task就是将src下面的所有js文件抓成ES5的语法。
  * 并且最终输出到dist文件夹中。
  * ![image-20201021134754342](13-webpack%E8%AF%A6%E8%A7%A3.assets/image-20201021134754342.png)
* 什么时候用grunt/gulp
  * 如果你的工程模块依赖非常简单，甚至是<u>没有用到模块化</u>的概念。
  * 只需要进行简单的合并、压缩，就使用grunt/gulp即可
  * 但是如果整个项目使用了模块化管理，而且<u>相互依赖非常强</u>，我们就可以使用更加强大的<u>webpack</u>了。
* 区分：
  * grunt/gulp更加强调的是前端流程的自动化，模块化不是它的核心。
  * webpack更加强调<u>模块化开发管理</u>，而文件压缩合并、预处理等功能，是他附带的功能。

# webpack安装

安装webpack首先需要安装Node.js，Node.js自带了软件包管理工具npm

查看自己的node版本：

```BASH
node -v
```

* 全局安装webpack（这里先指定版本号3.6.0，以为vue cli2依赖该版本）
  * `-g`表示全局安装

```bash
npm install webpack@3.6.0 -g
```

* 局部安装webpack（后续才需要）
  * `--save-dev`是<u>开发时依赖</u>，项目打包后不需要继续使用的。

```bash
cd 对应目录
npm install webpack@3.6.0 --save-dev
```

* Q：为什么全局安装后，还需要局部安装呢？
  * 在终端直接执行webpack命令，使用的全局安装的webpack
  * 当在package.json中定义了scripts时，其中包含了webpack命令，那么使用的是局部webpack。

# 准备工作

我们创建如下文件和文件夹：
![image-20201021150220691](13-webpack%E8%AF%A6%E8%A7%A3.assets/image-20201021150220691.png)

* 文件和文件夹解析：

  * dist文件夹：<u>distribution</u>用于存放打包之后的文件（发布的代码）

  * src文件夹：用于存放我们写的代码（开发的代码）

    * main.js：项目的入口文件，具体内容查看下面详情。

      ```javascript
      // 1.使用CommonJs的模块化规范
      const {add, mul} = require('./mathUtils.js')
      console.log(add(20, 30));
      console.log(mul(20, 30));
      
      // 2.使用ES6的模块化的规范
      import {name, height} from "./info";
      console.log(name);
      console.log(height);
      ```

    * mathUtils.js：定义了一些数学工具函数，可以在其他地方引用，并且使用。具体内容查看下面的详情。

      ```javascript
      function add(num1, num2) {
        return num1 + num2;
      }
      function mul(num1, num2) {
        return num1 * num2;
      }
      module.exports ={
        add,
        mul,
      }
      ```

    * index.html：浏览器打开展示的首页html

    * package.json：通过`npm init`生成的，npm包管理的文件

# js文件的打包

* 现在的js文件中使用了模块化的方式进行开发，他们可以直接使用吗？不能
  * 因为如果直接在index.html引入这两个js文件，浏览器并不识别其中的模块化代码。
  * 另外，在真实项目中当有许多这样的js文件时，我们一个个引用非常麻烦，并且后期非常不方便对它们进行管理。
* 可以使用webpack工具，对多个js文件进行打包。
  * 我们知道，webpack就是一个模块化的打包工具，所以它支持我们代码中写模块化，可以对模块化的代码进行处理。
  * 另外，如果在处理完所有模块之间的关系后，将多个js打包到一个js文件中，引入时就变得非常方便了。
* 打包指令如下：
  `webpack ./src/main.js ./dist/bundle.js`
  ![image-20201021150846889](13-webpack%E8%AF%A6%E8%A7%A3.assets/image-20201021150846889.png)

* 打包后会在dist文件夹下，生成一个`bundle.js`文件
  * 文件内容有些复杂，这里暂时先不看，后续再进行分析。
  * `bundle.js`文件，是webpack处理了项目直接文件依赖后生成的一个js文件，我们只需要将这个js文件在`index.html`中引入即可。
    ![image-20201021151058823](13-webpack%E8%AF%A6%E8%A7%A3.assets/image-20201021151058823.png)

## 入口和出口

* 我们考虑一下，如果每次使用webpack的命令都需要写上入口和出口作为参数，就非常麻烦，有没有一种方法可以将两个参数写到配置中，在运行时，直接读取呢？

* 当然可以，就是创建一个`webpack.config.js`文件

  * 注意entry代表入口，output代表出口，path需要**绝对路径**，所以我们使用node的一个path模块，获取当前路径。

  ```javascript
  const path = require('path');
  
  module.exports = {
    entry: './src/main.js',
    output: {
      path: path.resolve(__dirname, 'dist'), //动态获取路径, resolve是拼接
      filename: 'bundle.js',
    },
  }
  ```

## 局部安装webpack

* 目前，我们使用的webpack是全局的webpack，如果我们想使用局部来打包呢？
  * 因为一个项目往往依赖<u>特定</u>的webpack版本，全局的版本可能和当前项目的webpack版本不一样，导致打包出现问题。
  * 所以通常一个项目，都要自己局部的webpack。
* 第一步，项目中需要安装自己局部的webpack
  * 这里我们让局部安装webpack3.6.0
  * Vue CLI3中已经升级到webpack4，但是它将配置文件隐藏了起来，所以查看起来不是很方便。
    `npm install webpack@3.6.0 --save-dev`
* 第二步，通过`node_modules/.bin/webpack`启动webpack打包
  ![image-20201021160216213](13-webpack%E8%AF%A6%E8%A7%A3.assets/image-20201021160216213.png)

# package.json中定义启动

* 但是，每次执行都敲这么一长串有没有觉得不方便呢？

  * 其实，我们可以在package.json的scripts中定义自己的执行脚本。

* package.json中的scripts脚本在执行时，会按照一定的顺序寻找命令对应的位置。

  * 首先会寻找<u>**本地**</u>的node_modules/.bin路径中对应的命令。

  * 如果没有找到，会去全局的环境变量中寻找。

  * 如何执行我们的build指令呢？

    ![image-20201021160700217](13-webpack%E8%AF%A6%E8%A7%A3.assets/image-20201021160700217.png)
    接着在命令行输入`npm run build`，这相当于替换了webpack.

# 什么是loader

* loader是webpack中一个非常核心的概念。
* webpack用来做什么呢？
  * 在我们之前的实例中，我们主要是用webpack来<u>处理我们写的js代码</u>，并且webpack会自动<u>处理js之间相关的依赖</u>。
  * 但是，在开发中我们不仅仅有基本的js代码处理，我们也需要加载css、图片，也包括一些高级的，比如将ES6转成ES5代码，将TypeScript转成ES5代码，将scss、less转成css等等。
  * 对于webpack本身的能力来说，<u>对于这些转化是不支持的</u>。
  * 那怎么办呢？给webpack扩展对应的loader就可以了。
* loader使用过程：
  1. 通过npm安装需要使用的loader
     https://webpack.docschina.org/loaders/#styling去官网寻找对应的loader进行安装
  2. 中`webpack.config.js`中的modules关键字下进行配置

## css文件处理-准备工作

* 项目开发过程中，我们必然需要添加很多的样式，而样式我们往往写到一个单独的文件中。
  ![image-20201021170633337](13-webpack%E8%AF%A6%E8%A7%A3.assets/image-20201021170633337.png)

  * 在src目录中，创建一个css文件，其中创建一个css文件，其中创建一个normal.css文件。

  * 我们也可以重新组织文件的目录结构，将零散的js文件放在一个js文件夹中。

  * normal.css中的代码很简单，就是将body设置为red

    ```CSS
    body {
        background-color: red;
    }
    ```

* 但是，这个时候样式并不会生效，因为我们没有引用它。

  * webpack也不可能找到它，因为我们只有一个入口，webpack会从入口开始查找其它依赖的文件。

* 在入口文件中引用：
  ![image-20201021170745207](13-webpack%E8%AF%A6%E8%A7%A3.assets/image-20201021170745207.png)

## css文件处理-打包报错信息

重新打包，会出现如下错误
![image-20201021170907663](13-webpack%E8%AF%A6%E8%A7%A3.assets/image-20201021170907663.png)

这个错误信息告诉我们：加载normal.css文件必须有对应的loader。

## css文件处理-`css-loader`

* webpack[官网](https://webpack.docschina.org/loaders/css-loader/)，我们可以找到如下关于样式的loader使用方法：

  > ## 快速开始 
  >
  > 首先，你需要先安装 `css-loader`：
  >
  > ```console
  > npm install --save-dev css-loader
  > ```
  >
  > 然后把 loader 引用到你 `webpack` 的配置中。如下所示：
  >
  > **file.js**
  >
  > ```js
  > import css from 'file.css';
  > ```
  >
  > **webpack.config.js**
  >
  > ```js
  > module.exports = {
  >   module: {
  >     rules: [
  >       {
  >         test: /\.css$/i,
  >         use: ['style-loader', 'css-loader'],
  >       },
  >     ],
  >   },
  > };
  > ```

* 按照官方配置webpack.config.js文件

  * 注意：配置中有一个style-loader，我们并不知道它是什么，所以可以暂时不进行配置。

* 重新打包项目：

* 但是，运行index.html，我们会发现样式<u>并没有生效</u>。

  * 原因是css-loader只负责加载css文件，但是不负责将css具体样式嵌入到文档中。
  * 这个时候，我们还需要一个<u>style-loader</u>帮助我们处理。

## css文件处理-style-loader

> ## 快速开始 
>
> 首先，你需要安装 `style-loader`：
>
> ```console
> npm install --save-dev style-loader
> ```
>
> 推荐将 `style-loader` 与 [`css-loader`](https://webpack.docschina.org/loaders/css-loader/) 一起使用
>
> 然后把 loader 添加到你的 `webpack` 配置中。比如：
>
> **style.css**
>
> ```css
> body {
>   background: green;
> }
> ```
>
> **component.js**
>
> ```js
> import './style.css';
> ```
>
> **webpack.config.js**
>
> ```js
> module.exports = {
>   module: {
>     rules: [
>       {
>         test: /\.css$/i,
>         use: ['style-loader', 'css-loader'],
>       },
>     ],
>   },
> };
> ```

* 注意：`style-loader`需要放在`css-loader`的前面
  * 因为webpack在读取使用的loader的过程中，是按照<u>从右向左</u>的顺序读取的。

## less文件处理-准备工作

* 如果我们希望在项目中使用less,scss,stylus来写样式，webpack是否可以帮助我们处理呢？

  * 这里我们以less为例，其他也是一样的。

* 我们还是先创建一个less文件，依然放在css文件夹中

  ```less
  @fontSize: 50px;
  @fontColor: orange;
  
  body {
    font-size: @fontSize;
    color: @fontColor;
  }
  ```

## less文件处理-less-loader

* 继续在[官方](https://webpack.docschina.org/loaders/less-loader/)中查找，我们会找到less-loader相关的使用说明

* 首先，还是需要安装对应的loader

  * 注意：我们这里还安装了less，因为webpack会使用less对less文件<u>进行编译</u>
    `$ npm install less less-loader --save-dev`

* 其次，修改对应的配置文件

  * 添加一个rules选项，用于处理`.less`文件

    ```javascript
    {
      test: /\.less$/,
      use: [{
        loader: 'style-loader'
      }, {
        loader: 'css-loader'
      }, {
        loader: 'less-loader', // 将 Less 文件编译为 CSS 文件
      }],
    },
    ```

> ## 快速开始 
>
> 首先，你需要先安装 `less` 和 `less-loader`：
>
> ```console
> $ npm install less less-loader --save-dev
> ```
>
> 然后将该 loader 添加到 `webpack` 的配置中去，例如：
>
> **webpack.config.js**
>
> ```js
> module.exports = {
>   module: {
>     rules: [
>       {
>         test: /\.less$/,
>         loader: 'less-loader', // 将 Less 文件编译为 CSS 文件
>       },
>     ],
>   },
> };
> ```

## 图片文件处理-资源准备阶段

* 首先，我们在项目中加入两张图片：

  * 一张较小的图片`test.jpg`(小于8kb)，一张较大的图片`timg.jpg`(大于8kb)
  * 待会我们会针对这两张图片进行不同的处理。

* 我们先考虑在css样式中引用图片的情况，所以更改了normal.css中的形式：

  ```css
  body {
      background: url("../img/test.jpg");
  }
  ```

* 如果直接打包，会出现如下问题：
  ![image-20201021192615410](13-webpack%E8%AF%A6%E8%A7%A3.assets/image-20201021192615410.png)

## 图片文件处理-url-loader

* 图片处理，我们使用url-loader来处理，依然先安装url-loader
  `$ npm install url-loader --save-dev`

* 修改`webpack.config.js`配置文件

  ```javascript
  {
    test: /\.(png|jpg|gif|jpeg)$/i,
    use: [
      {
        loader: 'url-loader',
        options: {
          // limit: 8192,
          // 当加载的图片，小于limit时，会将图片编译成base64字符串形式。
          // 当大于limit时，需要使用file-loader模块进行加载
          limit: 10000,
        },
      },
    ],
  },
  ```

* 再次打包，运行index.html，就会发现背景图片显示出来了。
  * 仔细观察，会发现背景图是通过`base64`显示出来的
  * 这也是limit的属性作用，当图片小于8kb时，会对图片进行base64编码
    ![image-20201021193113985](13-webpack%E8%AF%A6%E8%A7%A3.assets/image-20201021193113985.png)

## 图片文件处理-file-loader

* 那么问题来了，如果图片大于8kb呢？我们将背景图片改为`timg.jpg`
  * 这次因为大于8kb，会通过`file-loader`进行处理，但是我们的项目中并没有file-loader
    ![image-20201021193334749](13-webpack%E8%AF%A6%E8%A7%A3.assets/image-20201021193334749.png)
* 所以，我们安装file-loader
  `npm install file-loader@3.0.1 --save-dev`

* 再次打包，会发现`dist`文件夹下多了个图片文件
  ![image-20201021193500638](13-webpack%E8%AF%A6%E8%A7%A3.assets/image-20201021193500638.png)

## 图片文件处理-修改文件名称

* 我们发现webpack自动帮助我们生成一个非常长的名字
  * 这是一个32位hash值，目的是<u>防止名字重复</u>。
  * 但是，真实开发中，我们可能对打包的图片名字有一定的要求。
  * 比如，将所有的图片放在一个文件夹中，跟上图片原来的名称，同时也要防止重复。
* 所以，我们可以在options中添加上如下选项：
  * `[img]`：文件要打包到的文件夹
  * `[name]`：获取图片原来的名字，放在该位置
  * `[hash:8]`：为了防止图片名称冲突，依然使用hash，但是我们只保留8位
  * `[ext]`：使用图片原来的扩展名
    ![image-20201021194232669](13-webpack%E8%AF%A6%E8%A7%A3.assets/image-20201021194232669.png)
* 但是，我们发现图片并没有显示出来，这是因为<u>图片使用的路径不正确</u>。
  * 默认情况下，webpack会将生成的路径直接返回给使用者
  * 但是，我们整个程序是打包在dist文件夹下的，所以这里我们需要在路径下载添加一个`dist/`
    ![image-20201021194253412](13-webpack%E8%AF%A6%E8%A7%A3.assets/image-20201021194253412.png)

# ES6语法处理

* 如果仔细阅读webpack打包的js文件，发现写的<u>ES6语法并没有转成ES5</u>，那么就意味着可能一些对ES6还不支持的浏览器没有办法很好的运行我们的代码、
  ![image-20201022083202317](13-webpack%E8%AF%A6%E8%A7%A3.assets/image-20201022083202317.png)

* 在前面我们说过，如果希望将ES6的语法转成ES5，那么就需要使用`babel`。

  * 而在webpack中，我们直接使用babel对应的loader即可，[官网](https://webpack.docschina.org/loaders/babel-loader/)可得

    ```bash
    npm install --save-dev babel-loader@7 babel-core babel-preset-es2015
    ```

* 配置`webpack.config.js`文件

  ```javascript
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /(node_modules|bower_components)/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['es2015']
          }
        }
      }
    ]
  }
  ```

* 重新打包，查看`bundle.js`文件，发现其中的内容变成了ES5的语法
  ![image-20201022084333571](13-webpack%E8%AF%A6%E8%A7%A3.assets/image-20201022084333571.png)

