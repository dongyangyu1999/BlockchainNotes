# Vuex是做什么的？

* 官方解释：Vuex是一个专为Vue.js应用程序开发的**状态管理模式**。
  * 它采用<u>集中式存储管理</u>应用的所以组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。
  * Vuex也集成到Vue的官方调试工具`devtools extension`，提供了诸如零配置的`time-travel`调试，状态快照导入导出等高级调试功能。

* **状态管理**到底是什么？
  * **状态管理模式、集中式存储管理**这些名词听起来就非常高大上，让人捉摸不透。
  * 其实，你可以简单的将其看成把需要多个组件<u>共享的变量</u>全部存储在一个对象里面。
  * 然后，将这个对象放在顶层的Vue实例中，让其他组件可以使用。
  * 那么，多个组件是不是就可以共享这个对象中的所有变量属性了呢？
* Vuex就是为了提供一个可以<u>在多个组件间共享状态的插件</u>，用它就可以了。
  * 当然，我们自己也可以封装实现一个对象，保证它里面所有属性都做到响应式，但是比较麻烦。

## 管理什么状态呢？

* 什么状态需要我们在多个组件间共享呢？
  * 比如多个界面间的共享问题。
  * 比如用户的登录状态、用户名称、头像、地理位置信息等
  * 比如商品的收藏、购物车中的物品等等。
  * 这些状态信息，我们都可以放在统一的地方，对它们进行保存和管理，而且它们还是响应式的。

# 单界面的状态管理

我们知道，要在单个组件中进行状态管理是一件非常简单的事情，看下图
![image-20201101085323824](19-Vuex%E8%AF%A6%E8%A7%A3.assets/image-20201101085323824.png)

图中三个东西，如何理解呢？

* State；这是我们的状态。（姑且当作是data中的属性）
* View：视图层，可以针对State的变化，显示不同的信息。
* Actions：这里的Actions主要是用户的各种操作：点击、输入等等，会导致状态发生改变。

![image-20201101092308672](19-Vuex%E8%AF%A6%E8%A7%A3.assets/image-20201101092308672.png)



# 多界面状态管理

* Vue已经帮我们做好了单个界面的状态管理，但是如果是多个界面呢？
  * 多个视图都依赖同一个状态（一个状态改了，多个界面需要进行更新）
  * 不同界面的Actions都想修改同一个状态（如Home.vue需要修改，Profile.vue也需要修改这个状态）
* 也就是说对于某些状态（state1/state2/state3）来说只属于我们某一个视图，但是也有一些状态（state A/state B/state C）属于多个视图共同想要维护的。
  * state1/state2/state3 放在自己的房间中，自己管理自己用，没问题。
  * 但是对于state A/state B/state C，我们希望交给一个<u>大管家</u>统一帮助我们管理。
  * 而Vuex就是为我们提供这个大管家的工具。
* 全局单例模式（大管家）
  * 我们现在要做的就是将共享的状态抽取出来，交给我们的大管家，统一进行管理。
  * 之后，每个视图，按照我们**规定好的规定**，进行访问和修改等操作。
  * 这就是Vuex背后的基本思想。

# Vuex状态管理图例

![image-20201101093615656](19-Vuex%E8%AF%A6%E8%A7%A3.assets/image-20201101093615656.png)

## 简单案例

![image-20201101100453604](19-Vuex%E8%AF%A6%E8%A7%A3.assets/image-20201101100453604.png)

**使用Vuex的count**

![image-20201101100530483](19-Vuex%E8%AF%A6%E8%A7%A3.assets/image-20201101100530483.png)

# Vuex核心概念

Vuex有几个核心概念：

* State：存储状态数据
* Getters：从状态数据派生数据，相当于State的计算属性。
* Mutation：存储用于同步更改状态数据的方法，默认传入的参数为state。
* Action：存储用于异步更改状态数据，但不是直接更改，而是通过触发Mutation方法实现，默认参数为context。
* Module : Vuex模块化。

## State单一状态树

* Vuex提出使用单一状态树，什么是单一状态树？
  * Single Source of Truth，也可以翻译成**单一数据源**
* 我们来看一个生活中的例子。
  * 我们知道，国内我们有很多的信息需要被记录，比如上学时的个人档案，工作后的社保记录，公积金记录，结婚后的婚姻信息，以及其他相关的户口、医疗、文凭、房产记录等等（还有很多信息）。
  * 这些信息被分散在很多地方进行管理，有一天你需要办某个业务时（比如入户某个城市），你会发现你需要到各个对应的工作地点去打印、盖章各种资料信息，最后到一个地方提交证明你的信息无误。
  * 这种保存信息的方案，不仅仅低效，而且很不方便管理，以及日后的维护也是一个庞大的工作（需要大量的各个部门的人力来维护）。

* 这个和我们在应用开发中比较类似：
  * 如果你的状态信息是保存到多个Store对象中的，那么之后的管理和维护等都会变得非常困难。
  * 所以Vuex也使用了<u>单一状态树</u>来管理应用层级的全部状态。
  * 单一状态树能够让我们用最直接的方式找到某个状态的片段，而且在之后的维护和测试过程中，也可以非常方便的管理和维护。



## Getters基本使用

* 有时候，我们需要从store中获取一些state变异后的状态，比如下面的store中：
  ![image-20201101105037800](19-Vuex%E8%AF%A6%E8%A7%A3.assets/image-20201101105037800.png)

### Getters作为参数和传递参数

* 如果我们已经有了一个获取所有年龄大于20岁学生列表的getters，那么代码可以这样来写：
  ![image-20201101105208468](19-Vuex%E8%AF%A6%E8%A7%A3.assets/image-20201101105208468.png)
* getters默认是<u>不能传递参数</u>的，如果希望传递参数，那么只能让getters本身**返回另一个函数**。
  * 比如上面的案例中，我们希望根据ID获取用户的信息。
    ![image-20201101105352751](19-Vuex%E8%AF%A6%E8%A7%A3.assets/image-20201101105352751.png)

## Mutations状态更新

Vuex的store状态的更新唯一方式：**<u>提交Mutation</u>**

* mutations主要包括两部分：

  * 字符串的**事件类型 (type)**
  * 一个**回调函数 (handler)**，该回调函数的第一个参数就是state。

* mutations的定义方式：

  ```js
  mutations: {
    // 方法
    increment(state) {
      state.counter++;
    },
    decrement(state) {
      state.counter--;
    }
  },
  ```

* 通过mutations更新

  ```js
  methods: {
    addition() {
      this.$store.commit('increment')
    },
    subtraction() {
      this.$store.commit('decrement')
    },
  }
  ```

### Mutations传递参数

* 在通过mutations更新数据的时候，有可能我们希望携带一些**额外的参数**

  * 参数被称为是mutation的载荷（Payload）

* Mutations中的代码：

  ```js
  // vuex中
  incrementCount(state, count){
    state.counter += count
  },
      
  // 组件中
  methods: {
    addCount(count){
      this.$store.commit('incrementCount', count)
    },
  }
  ```

* 但是如果参数不是一个呢？

  * 比如我们有很多参数需要传递。

  * 这个时候，我们通常会以<u>对象</u>的形式传递，也就是payload是一个对象。

  * 这个时候可以再从对象中取出相关的信息。

    ```JS
    // vuex中
    changeCount(state, payload) {
      state.count = payload.count
    }
    
    // 组件中
    methods: {
      changeCountFuc() {
        this.$store.commit('changeCount', {count: 0})
      }
    }
    
    ```

### Mutations提交风格

* 上面通过**commit**进行提交是一种普遍的方式

* Vue还提供了另外一种风格，它是一个包含type属性的对象

  ```js
  this.$store.commit({
    type: 'changeCount',
    count: 100, // 传的是一个对象
  })
  ```

* Mutations中的处理方式是将整个commit的**对象**作为payload使用，所以代码没有改变，依然如下

  ```js
  changeCount(state, payload){
    state.counter += payload.count // 因为传递的是对象
  },
  ```

### Mutations响应规则

* Vuex的store中的state是**响应式**的，当state中的数据发生改变时，Vue组件会自动更新。

* 这就要求我们必须遵守一些规则：

  * 提前给store中初始化所需的属性
  * 当给state中的对象添加属性时，使用下面的方式：
    * 方式一：使用`Vue.set(obj, 'newProp', 123)`
    * 方式二：用新对象给旧对象重新赋值

* 看个例子，当我们点击更新信息时，界面没有发生对应改变

  ![image-20201101145539723](19-Vuex%E8%AF%A6%E8%A7%A3.assets/image-20201101145539723.png)

* 如何才能让它改变？

  * 下方的方式一和方式二都可以让state中的属性是响应式的。
    ![image-20201101145719241](19-Vuex%E8%AF%A6%E8%A7%A3.assets/image-20201101145719241.png)

### Mutations类型常量 - 概念

* 我们来考虑下面的问题：
  * 在mutations中，我们定义了很多事件类型（也就是其中的方法名称）
  * 当我们的项目增大时，Vuex管理的状态越来越多，需要更新状态的情况越来越多，那么意味着Mutation中的方法越来越多。
  * 方法过多，使用者需要花费大量的经历去记住这些方法，甚至是多个文件间来回切换，查看方法名称，甚至如果不是复制的时候，可能还会出现写错的情况。

**代码**

![image-20201101152145578](19-Vuex%E8%AF%A6%E8%A7%A3.assets/image-20201101152145578.png)

### Mutations同步函数

* 通常情况下，Vuex要求我们Mutation中的方法**必须是同步方法**。
  * 主要的原因是当我们使用devtools时，devtools可以帮助我们捕捉mutation的快照。
  * 但是如果是异步操作，那么devtools将不能很好的追踪这个操作什么时候会被完成。

* 因此，通常情况下，不要在mutations中进行异步操作

## Actions的基本定义

* 我们强调，不要在Mutations中进行异步操作。
  * 但是某些情况下，我们确实希望在Vuex中进行一些异步操作，比如网络请求，必然是异步，那此时我们该怎么处理呢？
* Actions类似于Mutations，但是是用来替换Mutations**进行异步操作的**。

* Action的基本使用代码如下:
  ![image-20201102083237150](19-Vuex%E8%AF%A6%E8%A7%A3.assets/image-20201102083237150.png)
* context是什么?
  * context是和store对象具有<u>相同方法和属性</u>的对象.
  * 也就是说, 我们可以通过context去进行commit相关的操作, 也可以获取`context.state`等.
  * 但是注意, 这里它们并不是同一个对象, 为什么呢? 我们后面学习Modules的时候, 再具体说.
* 这样的代码是否多此一举呢?
  * 我们定义了actions, 然后又在actions中去进行commit, 这不是脱裤放屁吗?
  * 事实上并不是这样, 如果在Vuex中有异步操作, 那么我们就可以在actions中完成了.

### Action的分发

* 在Vue组件中, 如果我们调用action中的方法, 那么就需要使用dispatch
  ![image-20201102083415000](19-Vuex%E8%AF%A6%E8%A7%A3.assets/image-20201102083415000.png)
* 同样的，也是支持传递payload
  ![image-20201102083428147](19-Vuex%E8%AF%A6%E8%A7%A3.assets/image-20201102083428147.png)

### Action返回的Promise

* 前面我们学习ES6语法的时候说过, Promise经常用于异步操作.
  
* 在Action中, 我们可以将异步操作放在一个Promise中, 并且在成功或者失败后, 调用对应的resolve或reject.
  
* OK, 我们来看下面的代码:
  ![image-20201102083519387](19-Vuex%E8%AF%A6%E8%A7%A3.assets/image-20201102083519387.png)

  ![image-20201102083525849](19-Vuex%E8%AF%A6%E8%A7%A3.assets/image-20201102083525849.png)



## Modules的使用

* Modules是模块的意思，为什么在Vuex中我们要使用模块呢？
  * Vue使用单一状态树，那么也意味着很多状态都会交给Vuex来管理。
  * 当应用变得非常复杂时，store对象就有可能变得相当臃肿。
  * 为了解决这个问题，Vuex允许我们将store分割成模块（Module），而每个模块拥有自己的state、mutations、actions、getters等等

* 那按照什么样的方式来组织模块呢？看下方代码
  ![image-20201101162146763](19-Vuex%E8%AF%A6%E8%A7%A3.assets/image-20201101162146763.png)

### Modules局部状态

上面的代码中, 我们已经有了整体的组织结构, 下面我们来看看具体的局部模块中的代码如何书写.

* 我们在moduleA中添加state、mutations、getters
* mutations和getters接收的第一个参数是局部状态对象

![image-20201102082641727](19-Vuex%E8%AF%A6%E8%A7%A3.assets/image-20201102082641727.png)

* 注意:
  * 虽然, 我们的doubleCount和increment都是定义在对象内部的.
  * 但是在调用的时候, 依然是通过this.$store来直接调用的.



### Actions的写法

* actions的写法呢? 接收一个context参数对象
  * 局部状态通过 `context.state` 暴露出来，根节点状态则为 `context.rootState`
    ![image-20201102082832823](19-Vuex%E8%AF%A6%E8%A7%A3.assets/image-20201102082832823.png)
  * 如果getters中也需要使用全局的状态, 可以接受更多的参数
    ![image-20201102082846291](19-Vuex%E8%AF%A6%E8%A7%A3.assets/image-20201102082846291.png)

* 注，我们通常使用`context`代替上方的`{state, getters, rootState}`，上方的是ES6的对对象解构赋值语法

  ```js
  const obj = {
    name: 'why',
    age: 18,
    height: 1.88
  }
  
  // 平常是一个一个创建并赋值
    const name = obj.name
    const height = obj.height
  
  // ES6的对象解构赋值
    const {name, age, height} = obj
  // 会按照对应名称去对应赋值
    const {name, height，age} = obj // obj中的age不会按照顺序赋给height，而是赋给相同名称的age
  ```



# 项目结构

当我们的Vuex帮助我们管理过多的内容时, 好的项目结构可以让我们的代码更加清晰.

将他们进行抽离。

![image-20201102083612143](19-Vuex%E8%AF%A6%E8%A7%A3.assets/image-20201102083612143.png)

![image-20201102084555067](19-Vuex%E8%AF%A6%E8%A7%A3.assets/image-20201102084555067.png)