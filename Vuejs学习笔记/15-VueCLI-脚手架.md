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

