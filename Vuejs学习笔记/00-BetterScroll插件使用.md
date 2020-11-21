> 21-项目开发中BetterScroll插件的[扩展](https://github.com/Dongyang-Yu/BlockchainNotes/blob/main/Vuejs%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/21-%E9%A1%B9%E7%9B%AE%E5%BC%80%E5%8F%91.md#better-scroll%E6%BB%9A%E5%8A%A8%E6%8F%92%E4%BB%B6)

# 介绍

在移动端，如果你使用过 `overflow: scroll` 生成一个滚动容器，会发现它的滚动是比较卡顿，呆滞的。为什么会出现这种情况呢？

因为我们早已习惯了目前的主流操作系统和浏览器视窗的滚动体验，比如滚动到边缘会有回弹，手指停止滑动以后还会按惯性继续滚动一会，手指快速滑动时页面也会快速滚动。而这种原生滚动容器却没有，就会让人感到卡顿。



# 安装

`npm install better-scroll –save`进行安装，官方文档[指路](https://better-scroll.github.io/docs/zh-CN/)，笔记[在这](https://github.com/Dongyang-Yu/BlockchainNotes/blob/main/Vuejs%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/00-BetterScroll%E6%8F%92%E4%BB%B6%E4%BD%BF%E7%94%A8.md)

# 起步

BetterScroll 最常见的应用场景是列表滚动，我们来看一下它的 html 结构。

```html
<div class="wrapper">
  <ul class="content">
    <li>...</li>
    <li>...</li>
    ...
  </ul>
  <!-- 这里可以放一些其它的 DOM，但不会影响滚动 -->
</div>
```

上面的代码中 BetterScroll 是**作用在外层 wrapper 容器上**的，**滚动的部分是 content 元素**。

这里要注意的是，BetterScroll 默认处理容器（wrapper）的第一个子元素（content）的滚动，其它的元素都会被忽略。

# 基本使用解析

我们使用1.15版本的[bscroll.js文件](https://github.com/ustbhuangyi/better-scroll/blob/v1.15.0/dist/bscroll.js)进行测试。

## 上拉加载pullUpLoad

> 上拉加载更多，<u>只能加载一次</u>，[pullUpLoad官方文档](https://better-scroll.github.io/docs/zh-CN/plugins/pullup.html#pullupload-%E9%80%89%E9%A1%B9%E5%AF%B9%E8%B1%A1)

`pullUpLoad`选项，用来配置上拉加载功能。当设置为 true 或者是一个 Object 的时候，可以开启上拉加载，可以配置离底部距离阈值（threshold）来决定开始加载的时机

```
options.pullUpLoad = {
  threshold: -20 // 在上拉到超过底部 20px 时，触发 pullingUp 事件
}

this.scroll = new BScroll(this.$refs.wrapper, options)
```

监听 pullingUp 事件，加载新数据。

```js
this.scroll.on('pullingUp', () => {
  loadData()
    .then((newData) => {
      this.data.push(newData)
  })
})
```

## 索引列表probeType

> 索引列表，首先需要在滚动过程中实时监听滚动到哪个索引的区域了，来更新当前索引。在这种场景下，我们可以使用`probeType`选项，当此选项设置为 3 时，会在整个滚动过程中实时派发 scroll 事件。从而获取滚动过程中的位置。

选项设置：

* 0,1都是不侦测实时的位置
* 2：在手指滚动的过程中侦测，手指离开后的惯性滚动过程不侦测
* 3：只要是滚动的，都侦测

```js
options.probeType = 3
this.scroll = new BScroll(this.$refs.wrapper, options)

this.scroll.on('scroll', (pos) => {
  const y = pos.y

  for (let i = 0; i < listHeight.length - 1; i++) {
    let height1 = listHeight[i]
    let height2 = listHeight[i + 1]
    if (-y >= height1 && -y < height2) {
      this.currentIndex = i
    }
  }
})复制代码
```

当点击索引时，使用scrollToElement()方法滚动到该索引区域。

```js
scrollTo(index) {
  this.$refs.indexList.scrollToElement(this.$refs.listGroup[index], 0)
}
```



## 页面跳转scrollTo

scrollTo(x, y, time, easing, extraTransform)

- 参数：

  - {number} x 横轴坐标（单位 px）
  - {number} y 纵轴坐标（单位 px）
  - {number} time 滚动动画执行的时长（单位 ms）
  - {Object} easing 缓动函数，一般不建议修改，如果想修改，参考源码中的 `packages/shared-utils/src/ease.ts` 里的写法
  - 只有在你想要修改 CSS transform 的一些其他属性的时候，你才需要传入此参数，结构如下：

  ```js
  let extraTransform = {
    // 起点的属性
    start: {
      scale: 0
    },
    // 终点的属性
    end: {
      scale: 1.1
    }
  }
  ```

## Vue中注意事项

对于 Vue 中使用 BetterScroll，有一个需要注意的点是，因为在 Vue 模板中列表渲染还没完成时，是没有生成列表 DOM 元素的，所以需要在确保列表渲染完成以后，才能创建 BScroll 实例，因此在 Vue 中，初始化 BScroll 的最佳时机是 mouted 的 nextTick。

```js
// 在 Vue 中，保证列表渲染完成时，初始化 BScroll
mounted() {
   setTimeout(() => {
     this.scroll = new BScroll(this.$refs.wrapper, options)
   }, 20)
},
```



# 参考

https://juejin.im/post/6844903495171063821

