# 什么是路由

* 概念
  * 路由是网络工程里的一个术语
  * 路由（routing）就是通过互联的网络把信息从源地址传输到目的地址的活动。
* 解释
  * 路由器提供了两种机制：路由和转送
    * 路由是决定数据包从**来源**到**目的地**的路径
    * 转送将**输入端**的数据转移到合适的**输出端**
  * 路由中有一个非常重要的概念叫路由表。
    * 路由表本质上就是一个<u>映射表</u>，决定了数据包的指向。

# 后端路由阶段

> 后端路由：后端处理URL和页面之间的映射关系。

* 早期的网站开发整个HTML页面是由服务器来渲染的。
  * 服务器直接生成渲染好对应的HTML页面，返回给客户端进行展示。
* 但是网站的多个页面，服务器又该如何处理呢？
  * 一个页面有自己对应的网址，也就是URL。
  * URL会发送到服务器，服务器会通过正则对该URL<u>进行匹配</u>，并且最后交给一个Controller进行处理。
  * Controller进行各种处理，最终生成HTML或者数据，返回给前端。
  * 这就完成了一个IO操作。
* 上面的这种操作，就是<u>后端路由</u>。
  * 当我们页面中需要请求不同的**路径**内容时，交给服务器进行处理，服务器渲染好整个页面，并且将页面返回给客户端。
  * 这种情况下渲染好的页面，不需要单独加载任何的js和css，可以直接交给浏览器展示。
* 后端路由的缺点：
  * 一种情况是整个页面的模块由后端人员来编写和维护的。
  * 另一种情况是前端开发人员如果要开发页面，需要通过PHP和Java等语言来编写页面代码。
  * 而且通常情况下HTML代码和数据以及对应的逻辑会混在一起，编写和维护会非常麻烦。

# 前端路由阶段

> 前后端分离：后端只负责提供<u>数据</u>，不负责任何阶段的内容。
>
> 前端渲染：浏览器中显示的网页中的大部分内容，都是由<u>前端</u>写的js代码在浏览器中执行，最终渲染出来的网页。

* 前后端分离阶段：

  * 随着Ajax的出现，有了前后端分离的开发模式。
  * 后端只提供API来返回数据，前端通过Ajax获取数据，并且可以通过JavaScript将数据渲染到页面中。
  * 这样做最大的优点就是前后端责任清晰，后端专注于交互和可视化上。
  * 并且当移动端（iOS/Android）出现后，后端不需要进行任何处理，依然使用之前的一套API即可。

* 单页面富应用阶段（SPA: single page web application）

  > SPA整个页面只有一个html页面

  * SPA最主要的特点就是在前后端分离的基础上加了一层<u>前端路由</u>。
  * 也就是前端来维护一套路由规则。

* 前端路由的核心是什么呢？
  * 改变URL，但是页面不进行整体的刷新。
  * 如何实现？

## **补充知识**

### 后端渲染（SSR、服务端渲染Server Side Render）

早期网页前后端不分离的时候，后端渲染比较多，当我们访问网站的时候，会在服务器把相应的数据都处理好直接返回的是一个渲染好的页面，前端仅仅负责展示，这就是后端渲染。

### 前端渲染（SPA、单页面应用single page web application）

随着AJAX的出现，前后端分离的模式开始实行。后端<u>只需要提供相应的API</u>，不负责任何内容，前端拿到数据后进行处理，最终渲染到网页上。

# URL的hash

* URL的`hash`
  * URL的hash也就是锚点（`#`）,本质上是改变window.location的href属性。
  * 我们可以通过直接赋值location.hash来改变href，但是**页面不刷新**。
    ![image-20201027144221997](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201027144221997.png)

# HTML5的history模式：

## `pushState`

![image-20201027144344448](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201027144344448.png)

## `replaceState`

![image-20201027144508443](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201027144508443.png)

## `go`

* `history.go()`
  ![image-20201027144555212](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201027144555212.png)
* **补充说明**
  * 上面只演示了三个方法
  * 因为`history.back()`等价于`history.go(-1)`
  * `history.forward()`等价于`history.go(1)`
  * 这三个接口等同于浏览器界面的前进后退。

# 认识vue-router

* 目前前端流行的三大框架，都有自己的路由实现
  * Angular的ngRouter
  * React的ReactRouter
  * Vue的vue-router
* vue-router是Vue.js官方的路由插件，它和vue.js是深度集成的，适合用于构建单页面应用。
  * 可以访问其[官网](https://router.vuejs.org/zh)进行学习。

# 安装和使用vue-router

* 因为我们已经学习了webpack，后续开发我们主要通过<u>工程化的方式</u>进行开发。

  1. 安装vue-router

     ```bash
     npm install vue-router --save
     ```

  2. 在模块化工程中使用它(因为是一个插件，所以可以通过`Vue.use()`来安装路由功能)

     1. 导入路由对象，并且调用`Vue.use(VueRouter)`
     2. 创建路由实例，并且传入路由映射配置
     3. 在Vue实例中**挂载**创建的路由实例
     
     ```js
     // 配置路由相关的信息
     import VueRouter from 'vue-router'
     import Vue from 'vue'
     
     // 1.通过Vue.use（插件），安装插件
     Vue.use(VueRouter)
     
     // 2.创建VueRouter对象
     const routes = [
     ]
     const router = new VueRouter({
       //配置路由和组件之间的应用关系
       routes
     })
     
     // 3. 将router对象导出到Vue实例
     export default router
     ```

* 使用vue-router的步骤
  1. 创建路由组件
     ![image-20201028083819284](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201028083819284.png)
  2. 配置路由映射：组件和路径映射关系
     ![image-20201028083737659](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201028083737659.png)
  3. 使用路由：通过`<router-link>`和`<router-view>`.
     ![image-20201028083848344](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201028083848344.png)
     * `router-link`：该标签是一个`vue-router`中已经内置的组件，它会被渲染成一个`<a>`标签。
     * `<router-view>`：该标签会根据当前的路径，动态渲染出不同的组件。
       * 网页的其他内容，比如顶部的标题/导航，或者底部的一些版权信息等会和`router-view`处于**同一个等级**
       * 在路由切换时，切换的是`router-view`挂载的组件，其他内容不会发生改变。

# 路由的默认路径

* 有个细节可以考虑一下
  * 默认情况下，进入网站的首页，我们希望`<router-view>`渲染首页的内容。
  * 但是我们的实现中，默认没有显示首页组件，必须让用户点击才可以。
* 如何可以让路径<u>默认跳到首页</u>，并且`<router-view>`渲染首页组件呢？
  * 非常简单，我们只需要多配置一个映射即可。
    ![image-20201028085031789](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201028085031789.png)
* 配置解析：
  * 在`routes`中又配置了一个映射。
  * `path`配置的是根路径`'/' or ''`
  * `redirect`是重定向，也就是我们将根路径重定向到`/home`的路径下，这样就可以得到我们想要的结果了。

# HTML5的History模式

* 我们前面说过改变路径的方式有两种：

  * URL的hash
  * HTML的history
  * 默认情况下，路径的改变使用的URL的hash。

* 如果希望使用HTML的history模式，非常简单，进行如下配置即可。

  ```JS
  const router = new VueRouter({
    //配置路由和组件之间的应用关系
    routes,
    mode: 'history',
  })
  ```

![image-20201028090038928](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201028090038928.png)



# 补充：router-link

* 前面的`<router-link>`中，我们只使用了一个属性:`to`，<u>用于指定跳转的路径</u>。
* `<router-link>`还有一些**其他属性**：

## tag

* `tag`：tag可以指定`<router-link>`之后渲染成什么组件，比如上面的代码会被渲染成一个`<li>`元素而不是`<a>`

  ```JS
  <router-link to="/home" tag="button">首页</router-link>
  ```

## replace

* `replace`：replace不会留下history记录，所以指定replace的情况下，后退键<u>不能返回</u>到上一个页面中

  ```js
  <router-link to="/home" replace>首页</router-link>
  ```

## *实用 active-class

* (实用!) `active-class`：当`<router-link>`对应的路由匹配成功时，会自动给当前元素设置一个`router-link-active`的class，设置`active-class`可以修改默认的名称。

  * 在进行高亮显示的导航菜单或者底部tabbar时，会使用到该类
  * 但是通常不回修改类的属性，会直接使用默认的`router-link-active`即可

  ![image-20201028091322641](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201028091322641.png)

### 修改`linkActiveClass`

* 如果有多个link，都要改会很麻烦，vue提供了一个方式
  ![image-20201028091703356](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201028091703356.png)
  * 在`router`中配置路由是，可以增加属性`linkActiveClass`，就可以一次性改变所有的<u>类的属性名</u>
    ![image-20201028091902888](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201028091902888.png)
    ![image-20201028092008558](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201028092008558.png)

# 路由代码跳转

* 有时候，页面的跳转可能需要执行对应的JavaScript代码，这个时候，就可以使用第二种跳转方式了
* 比如，将代码修改成如下：
  * `this.$router.push`或者`this.$router.replace`

![image-20201028094032560](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201028094032560.png)

# *动态路由的使用

* 在某些情况下，一个页面的path路径可能是不确定的，比如我们进入用户界面时，希望是如下的路径：
  * `/user/zhangsan`等等
  * 除了有前面的`/user`之外，后面还跟上了用户的ID
  * 这种path和Component的匹配关系，我们称之为动态路由（也是路由传递的一种方式）。

```js
// index.js
{
  path: '/user/:abc',
  component: User,
},
```

```js
// App.vue
<router-link :to="'/user/' + userId " >用户</router-link>

<script>
export default {
  name: 'App',
  data() {
    return {
      userId: 'Ydy',
    }
  },
}
</script>
```

```js
// User.vue
<h2>{{$route.params.abc}}</h2>
```

# 认识路由的懒加载

* 官方解释
  * 当打包构建应用时，JavaScript包会变得非常大，影响页面加载
  * 如果我们能把不同路由对应的组件<u>分割成不同的代码块</u>，然后当路由被访问的时候才加载对应组件，这样就更加高效了。
* 通俗解释
  * 首先，我们知道路由中通常会定义很多不同的页面。
  * 这个页面最后被打包在哪里？一般情况下，是放在一个js文件中。
  * 但是页面这么多，放在一个js文件中，必然会造成这个<u>页面非常的大</u>。
  * 如果我们一次性从服务器请求下来这个页面，可能需要花费一定的时间，甚至用户的电脑上还会出现短暂空白的情况。
  * 如何避免这种情况呢？使用路由懒加载就可以了。

* 路由懒加载的作用：
  * 其主要作用是将路由对应的组件打包成一个个的js代码块。
  * <u>只有在这个路由被访问到的时候，才加载对应的组件</u>

![image-20201028135735779](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201028135735779.png)

## 懒加载的方式

* 方式一：结合Vue的异步组件和Webpack的代码分析。

  ```JS
  const Home = resolve => { require.ensure( [ ' . ./components/Home.vue'],() ={ resolve(require( ' ../ components /Home.vue' )) })};
  ```

* 方式二：AMD写法

  ```js
  const About = resolve => require( [ ' ../components/About.vue' ], resolve);
  ```

* 方式三：在ES6中，我们可以有更加简单的写法来组织Vue异步组件和Webpack的代码分割。

  ```JS
  const Home = () => import('../components/Home.vue')
  ```

  

# 认识嵌套路由

* 嵌套路由是一个很常见的功能。
  * 比如在home页面中，我们希望通过`/home/news`和`/home/messages`访问一些内容。
  * 一个路径映射一个组件，访问这两个路径也会分别渲染两个组件。
* 路径和组件的关系如下：
  ![image-20201028141248582](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201028141248582.png)
* 实现嵌套路由的两个步骤：
  1. 创建对应的子组件，并且在路由映射中<u>配置对应的子路由</u>。
  2. 在组件内部使用`<router-view>`标签

![image-20201028143014129](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201028143014129.png)

类似之前的路由，进行套娃。

* 注意，路径要加上主路由，比如此处的`/home`

![image-20201028143106391](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201028143106391.png)

效果如下：

![image-20201028143135859](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201028143135859.png)

## 传递参数

### 准备工作

* 为了演示传递参数，我们这里再创建了一个组件，并且将其配置好
  * 第一步：创建新的组件`Profile.vue`
  * 第二步：配置路由映射
  * 第三步：添加跳转的`<router-link>`

### 传递参数的方式

* 传递参数主要有两种类型：`params`和`query`
* `params`的类型：
  * 配置路由**格式**：`/router:id`
  * 传递的方式：在path<u>后面跟上对应的值</u>
  * 传递后形成的路径：`/router/123`，`router/abc`

* `query`的类型
  * 配置路由格式：`/router`，也就是普通配置
  * 传递的方式：对象中使用<u>query的key作为传递方式</u>
  * 传递后形成的路径：`/router?id=123`,`/router?id=abc`
* 如何使用它们呢？也有两种方式：`<router-link>`和JavaScript代码的方式

### 传递参数方式一：`<router-link>`

![image-20201028151633003](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201028151633003.png)

### 传递参数方式二：JavaScript代码

![image-20201028151900017](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201028151900017.png)



### 获取参数

* 获取参数通过`$route`对象获取的。
  * 在使用了`vue-router`的应用中，路由对象会被注入每个组件中，赋值为`this.$route`，并且当路由切换时，路由对象会被更新。
* 通过`$route`获取传递的信息如下
  ![image-20201028151353146](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201028151353146.png)

## `$route`和`$router`的区别

* `$router`为VueRouter的**实例**，想要导航到不同的URL，就需要使用`$router.push`方法
* `$route`为当前router**跳转对象**，里面可以获取name, path, query, params等

![image-20201029084358666](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201029084358666.png)



`$route`

![image-20201029082628171](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201029082628171.png)

![image-20201029082708436](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201029082708436.png)

# 为什么使用导航守卫

* 我们来考虑一个需求：在一个SPA应用中，如何改变网页的标题呢？
  * 网页标题是通过`<title>`来显示的，但是SPA只有一个固定的HTML，切换不同的页面时，标题并不会改变。
  * 但是我们可以通过JavaScript来修改`<title>`的内容`window.document.title='新的标题'`
  * 那么在Vue项目中，在哪里修改？什么时候修改比较合适呢？

* 普通的修改方式：
  * 我们比较容易想到的修改标题的位置是每一个路由对应的`组件.vue`文件中。
  * 通过`mounted`声明周期函数，执行对应的代码进行修改即可。
  * 但是当页面比较多时，这种方式不容易维护（以为需要在多个页面执行重复代码）。
* 更好的办法：**导航守卫**

## 什么是导航守卫

* vue-router提供的导航守卫主要用来<u>监听路由的进入和离开</u>
* vue-router提供了`beforeEach`和`afterEach`的**钩子函数**，它们会在路由即将改变前和改变后触发。

## 导航守卫的使用

* 我们可以利用`beforeEach`来完成标题的修改。
  * 首先，我们可以在钩子当中定义一些标题，可以利用`meta`来定义
  * 其次，利用导航守卫修改我们的标题。
* 导航钩子的三个参数解析：
  * `to`：即将要进入的目标的路由对象
  * `from`：当前导航即将要离开的路由对象
  * `next`：调用该方法后，才能进入下一个钩子。

![image-20201029093534148](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201029093534148.png)

## 导航守卫补充

* 补充一：如果是后置钩子，也就是`afterEach`，**不需要**主动调用`next()`函数。

* 补充二：上面我们使用的导航守卫，被称之为全局守卫。还有另外两种守卫：
  * 路由独享的守卫。
  * 组件内的守卫。
    [查看详情](https://router.vuejs.org/zh/guide/advanced/navigation-guards.html#%E8%B7%AF%E7%94%B1%E7%8B%AC%E4%BA%AB%E7%9A%84%E5%AE%88%E5%8D%AB)



# `vue-router-keep-alive`相关问题

* `keep-alive`是Vue内置的一个组件，可以使被<u>包含的组件保留状态</u>，或避免重新渲染。

  * 它们有两个非常重要的属性：

  * `include`：字符串或正则表达，只有匹配的组件会被缓存

  * `exclude`：字符串或正则表达式，任何匹配的组件**都不会**被缓存

    ```js
    <keep-alive exclude="Profile,User"> //不能加空格
      <router-view></router-view>
    </keep-alive>
    ```

* `router-view`也是一个组件，如果直接被包在`keep-alive`里面，所有路径匹配到的<u>视图组件都会被缓存</u>

  ```js
  <keep-alive>
    <router-view>
    	<!-- 所有路径匹配到的视图组件都会被缓存！ -->  
    </router-view>
  </keep-alive>
  ```

* 通过create声明周期函数来验证

# 案例：TabBar

实现思路：

1. 如果在页面下方有一个单独的TabBar组件，如何封装
   * 自定义TabBar组件，在App中使用
   * 让TabBar处于底部，并且设置相关的样式
   
2. TabBar中显示的内容由外界决定
   * 定义插槽
   * flex布局平分TabBar
   
3. 自定义TabBarItem，可以传入图片和文字
   * 定义TabBarItem，并且定义两个插槽：图片、文字。
   * 给两个插槽外层包装div，用于设置样式
   * 填充插槽，实现底部TabBar的效果
   
4. 传入高亮图片

   * 定义另外一个插槽，插入active-icon的数据
   * 定义一个变量`isActive`，通过v-show来决定是否显示对应的icon

5. TabBarItem绑定路由数据

   * 安装路由：`npm install vue-router --save`
   * 完成`router/index.js`的内容，以及创建对应的组件
   * `main.js`中注册router
   * APP中加入`<router-view>`组件

6. 点击item跳转到对应路由，并且动态决定`isActive`

   * 监听item的点击，通过`this.$router.replace()`替换路由路径

   * 通过`this.$route.path.indexOf(this.path) !== -1`来判断是否是active
     `indexOf() `方法可返回某个指定的字符串值在字符串中首次出现的位置。

     > 如果要检索的字符串值没有出现，则该方法返回 -1。

7. 动态计算active样式

   * 封装新的计算属性：`this.isActive?{'color': 'red'}:{}`



# 给路径起别名

> 相当于给绝对路径起个简短的名字

在`build/webpack.base.conf.js`中，如下图所示，用`@`代替`src`路径。



![image-20201031094326511](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201031094326511.png)

```js
resolve: {
  extensions: ['.js', '.vue', '.json'],
  alias: {
    '@': resolve('src'),
    'assets': resolve('src/assets'),
    'components': resolve('src/components'),
    'views': resolve('src/views'),
  }
},
```

## 导入组件

比如要导入`TabBar`，下方的代码就能修改成`components/tabbar/TabBar`

```
<script>
import TabBar from "./tabbar/TabBar";
import TabBarItem from "./tabbar/TabBarItem";
```

![image-20201031094410230](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201031094410230.png)

## DOM中路径要加`~`波浪号

```js
<img slot="item-icon" src="~assets/img/tabbar/home.svg" alt="">
```

不加波浪号找不到。

![image-20201031095328887](16-Vue-router%E8%AF%A6%E8%A7%A3.assets/image-20201031095328887.png)

