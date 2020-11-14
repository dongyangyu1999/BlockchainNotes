# 什么是Promise？

>  ES6中一个非常重要和好用的特性就是Promise

* Promise是**异步编程**的一种解决方案。

* 何时我们会处理异步事件呢？
  * 一种常见的场景就是网络请求。
  * 我们封装一个网络请求的函数，因为不能立即拿到结果（阻塞），所以不能像简单的3+4=7一样将结果返回。
  * 所以往往会传入另一个函数，在数据请求成功时，将数据通过传入的函数**回调**出去。
  * 所以只是一个简单的网络请求，那么这种方案不会给我们带来很大的麻烦。
* 但是，当网络请求非常复杂时，就会出现**回调地狱**(callback hell)。

## 网络请求的"回调地狱"

* 我们来考虑下面的场景（有夸张的成分）：

  * 我们需要通过一个url从服务器加载一个数据data1，data1包含了下一个请求的url2

  * 我们需要通过data1取出url2，从服务器加载数据data2，data2中包含了下一个请求的url3

  * 我们需要通过data2取出url3，从服务器加载数据data3，data3中包含了下一个请求的url4

  * 发送网络请求url4，获取最终的数据data4

    ![image-20201031135024496](18-Promise%E7%9A%84%E4%BD%BF%E7%94%A8.assets/image-20201031135024496.png)

* 上面的代码有什么问题吗？

  * 正常情况下，不回有什么问题，可以正常运行并且获取到我们需要的结果。
  * 但是，这样的代码难看且不易维护。
  * 我们更期望一种更加优雅的方式来进行这种异步操作。

* 怎么做呢？就是用**Promise**。

  * Promise可以以一种非常优雅的方式来解决这个问题。

看一个例子：

### 定时器的异步事件

* 我们来看看Promise最基本的语法。
* 这里，我们用一个定时器来模拟异步事件：
  * 假设下面的data是从网络上1秒后请求的数据
  * `console.log`就是我们的处理方式。
    ![image-20201031142502681](18-Promise%E7%9A%84%E4%BD%BF%E7%94%A8.assets/image-20201031142502681.png)
* 这是我们过去的处理方式，我们将它换成Promise代码
* 这个例子会让我们感觉多此一举
  * 首先，下面的Promise代码明显比上面的代码看起来还要复杂。
  * 其次，下面的Promise代码中包含的resolve、reject、then、catch都是些什么东西？
    ![image-20201031142528711](18-Promise%E7%9A%84%E4%BD%BF%E7%94%A8.assets/image-20201031142528711.png)

* 什么情况下会用到Promise？
  * 一般情况下，有异步操作时，使用Promise对这个异步操作进行<u>封装</u>

# Promise三种状态

* 首先，当我们开发中有异步操作时，就可以给异步操作包装一个Promise

  * 异步操作之后会有三种状态

* 三种状态分别如下：

  * pending：等待状态，比如正在进行网络请求，或者定时器没有到时间。
  * fulfill：满足状态，当我们主动回调了resolve时，就处于该状态，并且会回调`.then()`
  * reject：拒绝状态，当我们主动回调了reject时，就处于该状态，并且会回调`.catch()`

  <img src="18-Promise%E7%9A%84%E4%BD%BF%E7%94%A8.assets/image-20201031144052731.png" alt="image-20201031144052731" style="zoom: 67%;" />

# Promise链式调用

* 我们在看Promise的流程图时，发现无论是then还是catch都可以返回一个Promise对象。
* 所以，我们的代码其实是可以进行链式调用的。
* 这里我们直接通过Promise包装了一下新的数据，将Promise对象返回了
  * `Promise.resolve()`：将数据包装成Promise对象，并且在内部回调resolve()函数
  * `Promise.reject()`：将数据包装成Promise对象，并且在内部回调reject()函数

## 链式调用简写

* 简化版代码：

  * 如果我们希望数据直接包装成`Promise.resolve`，那么在then中可以直接返回数据
  * 注意下面的代码中。将`return Promise.resolve(data)`改成了`return data`
  * 结果是一样的

  ```js
  // 省略掉Promise.resolve
  new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('aaa');
    }, 1000)
  }).then(res => {
    console.log(res, '第一层的10行处理代码');
      
    return res + '111'
  //return Promise.resolve(res + '111')
  }).then(res => {
    console.log(res, '第二层的10行处理代码');
    return res +'222'
  }).then(res=>{
    console.log(res, '第三层的10行处理代码');
  })
  ```