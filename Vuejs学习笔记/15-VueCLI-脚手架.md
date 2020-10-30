# 什么是脚手架CLI

* 如果只是简单写几个Vue的Demo程序，那么就不需要Vue CLI。
* 但是如果要开发大型项目，那么你需要，并且必然需要使用Vue CLI。
  * 需要考虑代码目录结构、项目结构和部署、热加载、代码单元测试等。
  * 使用一些脚手架工具会使我们的效率变高，因为我们并不需要完全手动完成上述工作。
* CLI介绍
  * CLI是Command-Line Interface，翻译为**命令行界面**，但是俗称<u>脚手架</u>。
  * Vue CLI是一个官方发布的vue.js项目脚手架。
  * 使用vue-cli可以快速搭建Vue开发环境以及对应的webpack配置。

# Vue CLI使用前提 - Node

* 安装NodeJS和NPM
  * Node环境要求8.9以上或者更高版本
  * NPM全称是Node Package Manager
    * 是一个NodeJS包管理和分发工具，已经成为了非官方的发布Node模块（包）的标准。

> `cnpm`安装代替`npm`
>
> 由于国内直接使用`npm`的官方镜像，会非常慢。推荐使用`淘宝npm`镜像。
>
> 可以使用`cnpm`命令行工具代替默认的`npm`：
>
> `npm install -g cnpm --registry=https://registry.npm.taobao.org`
>
> 这样就可以使用`cnpm`命令来安装模块了：
>
> `cnpm install [name]`

# Vue CLI使用前提 - Webpack

* Vue.js官方脚手架工具就使用了Webpack模板

  * 对所有的资源会压缩等优化操作
  * 它在开发过程中提供了一套完整的功能，能够使得我们开发过程中变得高效。

* Webpack的全局安装

  `npm install webpack -g`

# Vue CLI的使用

* 安装Vue脚手架

  ```bash
  npm install -g @vue/cli
  ```

  > 注意：上面安装的是Vue CLI3的版本，如果想要按照Vue CLI2的方式初始化项目是不可以的。

## 拉取 2.x 模板 (旧版本)

Vue CLI >= 3 和旧版使用了相同的 `vue` 命令，所以 Vue CLI 2 (`vue-cli`) 被覆盖了。如果你仍然需要使用旧版本的 `vue init` 功能，你可以全局安装一个桥接工具：

```bash
npm install -g @vue/cli-init
# `vue init` 的运行效果将会跟 `vue-cli@2.x` 相同
vue init webpack my-project
```

## Vue CLI2 初始化项目

```bash
vue init webpack my-project
```

## Vue CLI3 初始化项目

```bash
vue create my-project
```



# CLI2 初始化项目

![image-20201024103154418](15-VueCLI-%E8%84%9A%E6%89%8B%E6%9E%B6.assets/image-20201024103154418.png)

```BASH
E:\Typora_notes\Vuejs代码\LearnVuejs03-1024>vue init webpack vuecli2test

? Project name vuecli2test
? Project description A Vue.js project
? Author Dongyang-Yu <*****@nau.edu>
? Vue build standalone
? Install vue-router? No
? Use ESLint to lint your code? Yes
? Pick an ESLint preset Standard
? Set up unit tests No
? Setup e2e tests with Nightwatch? No
? Should we run `npm install` for you after the project has been created? (recommended) npm

   vue-cli · Generated "vuecli2test".


# Installing project dependencies ...
# ========================
```

# CLI2目录结构解析

创建成功后，一般先看`package.json`文件，看里面的脚本命令。

![image-20201024103848964](15-VueCLI-%E8%84%9A%E6%89%8B%E6%9E%B6.assets/image-20201024103848964.png)

看build命令，使用的是`node`，node<u>本身为js提供了一个运行环境</u>，不像以前，需要创建html文件，将js文件引入然后用浏览器打开这么繁琐。

> node是基于`Chrome V8`引擎开发的能使`JavaScript`在服务器端运行的**运行时环境**（`runtime` `environment`）。
>
> `V8`采用即时编译技术（`JIT`），直接将`JavaScript`代码编译二进制代码。宏观上看，其步骤为`JavaScript`源码—>抽象语法树—>本地机器码，并且后一个步骤只依赖前一个步骤。
> 这与其他解释器不同，例如`Java`语言将源码编译成字节码，然后给`JVM`解释执行，字节码编译成本地机器码。
> `V8`不生成中间代码，一步到位，编译成机器码，`CPU`就开始执行了。比起生成中间码解释执行的方式，`V8`的策略省去了一个步骤，程序会更早地开始运行。并且执行编译好的机器指令，也比解释执行中间码的<u>速度更快</u>。不足的是，缺少字节码这个中间表示，使得代码优化变得更困难。

![image-20201024110109226](15-VueCLI-%E8%84%9A%E6%89%8B%E6%9E%B6.assets/image-20201024110109226.png)



## Vue程序运行过程

![image-20201024154931366](15-VueCLI-%E8%84%9A%E6%89%8B%E6%9E%B6.assets/image-20201024154931366.png)



## runtime-compiler和runtime-only的区别

![image-20201024155039467](15-VueCLI-%E8%84%9A%E6%89%8B%E6%9E%B6.assets/image-20201024155039467.png)

根据上方vue程序运行的过程，可知

* 左侧runtime-compiler是`template->ast->render->vdom->UI`；
* 右侧runtime-only是直接从`render->vdom->UI`

结论：

* runtime-only性能更高，代码量更少，这就是创建时`少6kb`的原因。

### 简单总结：

* 如果之后的开发中，依然使用template，就需要选择Runtime-Compiler
* 如果之后的开发中，使用的是`.vue`文件夹开发，那么可以选择Runtime-only

### render函数的使用

```js
const cpn = {
  template: `<div>{{message}}</div>`,
  data() {
    return {
      message: '我是组件message'
    }
  }
}

new Vue({
  el: '#app',
  // components: { App },
  // template: '<App/>'
  render: function (createElement) {
     // 1.普通语法： createElement('标签', {标签的属性}, [''])
     return createElement('h2', {class: 'box'}, ['hello world'])
     return createElement('h2',
       {class: 'box'},
       ['hello world', createElement('button', ['按钮'])])
    // 2. 传入组件对象
    return createElement(App);
  }
```

# npm run build命令执行过程解析

![image-20201024162007794](15-VueCLI-%E8%84%9A%E6%89%8B%E6%9E%B6.assets/image-20201024162007794.png)

# npm run dev过程解析

![image-20201024162144774](15-VueCLI-%E8%84%9A%E6%89%8B%E6%9E%B6.assets/image-20201024162144774.png)



# 认识Vue CLI3

* vue-cli 3与2版本有很大区别
  * 3是基于webpack4打造的，2还是webpack3
  * 3的设计原则是“0配置”，移除配置文件根目录下的`build`和`config`等目录
  * 3提供了vue ui命令，提供了<u>可视化配置</u>，更加人性化
  * 移除了`static`文件夹，新增了`public`文件夹，并且将`index.html`移动到`public`中

## 配置文件去哪了？

* UI方面的配置
  * 启动配置服务器：`vue ui`
    ![image-20201026155410794](15-VueCLI-%E8%84%9A%E6%89%8B%E6%9E%B6.assets/image-20201026155410794.png)

* 配置文件在`node_modules/@vue/cli-service`目录下
  ![image-20201026155556970](15-VueCLI-%E8%84%9A%E6%89%8B%E6%9E%B6.assets/image-20201026155556970.png)

* 修改配置的话在主目录下创建文件`vue.config.js`即可，默认会搜索该文件名进行配置
  ![image-20201026155800753](15-VueCLI-%E8%84%9A%E6%89%8B%E6%9E%B6.assets/image-20201026155800753.png)

