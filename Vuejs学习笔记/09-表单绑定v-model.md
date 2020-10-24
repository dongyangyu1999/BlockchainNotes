# 表单绑定v-model

* 表单控件在实际开发中是非常常见的，特别是对于用户信息的提交，需要大量的表单。
* Vue中使用`v-model`指令来实现<u>表单元素和数据的双向绑定</u>
* 案例分析：
  * 当我们在输入框输入内容时
  * 因为input中的v-model绑定了message，所以会实时将<u>输入的内容传递给message</u>，message会发生改变。
  * 当message发生改变时，因为上面我们使用Mustache语法，将message的值插入到DOM中，所以DOM会发生响应的改变。
  * 所以，通过v-model实现了双向的绑定。

```html
<div id="app">
<!--  <input type="text" v-model="message">-->
<!--  <input type="text" :value="message" @input="valueChange($event)">-->
  <input type="text" :value="message" @input="message=$event.target.value">
  <h2>{{message}}</h2>
</div>

<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      message: '你好啊',
    },
    methods: {
      valueChange(event) {
        this.message = event.target.value;
      }
    }
  })
</script>
```

当然，我们也可以用v-model用于textarea元素

```html
  <textarea v-model="message"></textarea>
  <p>输入的内容是：{{message}}</p>
```

> 注意：
>
> v-model是双向绑定，而`v-bind`只是单向的，如果想要实现双向，需要通过`event.target.value`来修改`message`的值达到双向修改的效果

# v-model原理

* v-model其实是一个**语法糖**，它的背后本质是包含两个操作：
  1. v-bind绑定一个value属性
  2. v-on指令给当前元素绑定input事件

# v-model结合radio类型使用

若需要单选按钮（即按钮间互斥），则在input标签中插入属性`name`，任命为相同值。

```html
<div id="app">
  <label for="male">
    <input type="radio" id="male" name="sex" value="男" v-model="sex" >男
  </label>
  <label for="female">
    <input type="radio" id="female" name="sex" value="女" v-model="sex">女
  </label>
  <h1>您选择的性别是：{{sex}}</h1>
</div>

<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      message: '你好啊',
      sex: '男',
    }
  })
</script>
```

## 复习label和input的关系

通过为input设置属性`id`，在label标签中设置`for=id`使label文本与对应的input结合起来。

> label与input的关系：**选择label标签时，浏览器会自动将焦点转移到个label相关的input上。**
>
> ```HTML
> <label for="male">
>  <input type="radio" id="male" name="sex" value="男" v-model="sex" >男
> </label>
> ```
>
> label与input相关联的优点：
>
> * 当用户聚焦到这个表单输入元素时，屏幕阅读器可以读出标签，让使用辅助技术的用户更容易理解应输入什么数据。
> * 点击关联的标签来<u>聚焦或者激活</u>这个输入元素，这扩大了元素的可点击区域，让包括使用触屏设备在内的用户更容易激活这个元素，获得很好的用户体验。
> * 说白了，也就是说点击文字也可以响应按钮



# v-model结合checkbox类型使用

* 复选框分为两种情况：单个勾选框和多个勾选框
* 单个勾选框：
  * v-model即为**布尔值**
  * 此时input的value并不影响v-model的值
* 多个勾选框
  * 当是多个复选框时，因为可以选中多个，所以对应的data中属性是一个**数组**。
  * 当选中某一个时，就会将input的value添加到数组中。

```html
<div id="app">
  <!-- 1.checkbox单选框-->
  <label for="license">
    <input type="checkbox" id="license" v-model="isAgree">同意协议
  </label>
  <h2>您选择的是：{{isAgree}}</h2>
  <button :disabled="!isAgree">下一步</button>
  <br>

  <!-- 2.checkbox多选框-->
    <input type="checkbox" value="篮球" v-model="hobbies">篮球
    <input type="checkbox" value="足球" v-model="hobbies">足球
    <input type="checkbox" value="乒乓球" v-model="hobbies">乒乓球
    <input type="checkbox" value="羽毛球" v-model="hobbies">羽毛球
  <h2>您的爱好是：{{hobbies}}</h2>

</div>
```

# v-model结合select类型使用

* 和checkbox一样，select也分单选和多选两种情况。
* 单选：只能选中一个值
  * v-model绑定的是一个值
  
  * 当我们选中option中的一个时，会将它对应的value赋值到`fruit`中
  
  * ```html
    <!--1. 选择一个-->
    <select name="abc" v-model="fruit">
        <option value="苹果" >苹果</option>
        <option value="香蕉" >香蕉</option>
        <option value="榴莲" >榴莲</option>
        <option value="葡萄" >葡萄</option>
    </select>
    <h2>您选择的水果是:{{fruit}}</h2>
    ```
* 多选：可以选中多个值。
  * v-model绑定的是一个数组
  
  * 当选中多个值时，将会将选中的option对应的value添加到数组`fruits`中
  
  * ```html
    <!--2. 选择多个 -->
    <select name="abc" v-model="fruits" multiple>
      <option value="苹果" >苹果</option>
      <option value="香蕉" >香蕉</option>
      <option value="榴莲" >榴莲</option>
      <option value="葡萄" >葡萄</option>
    </select>
    <h2>您选择的水果是:{{fruits}}</h2>
    <script src="../js/vue.js"></script>
    <script>
      const app = new Vue({
        el: '#app',
        data: {
          fruit: '香蕉',
          fruits: [],
        }
      })
    </script>
    ```
  
    ![image-20201016151725283](09-%E8%A1%A8%E5%8D%95%E7%BB%91%E5%AE%9Av-model.assets/image-20201016151725283.png)

# input中的值绑定概念

顾名思义，数据不是写死，而是动态绑定进行传递的。类似于v-bind。

```html
  <label v-for="item in originHobbies" :for="item">
    <input type="checkbox" :value="item" :id="item" v-model="hobbies">{{item}}
  </label>
  <h2>您的爱好是：{{hobbies}}</h2>
...
<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      hobbies: [], // 多选框 数组
      originHobbies: ['篮球', '足球', '乒乓球', '羽毛球', '台球', '高尔夫球'],
    }
  })
</script>
```

# v-model修饰符的使用

## lazy修饰符

`v-model.lazy="xxx"`

* 默认情况下，v-model默认是在input事件中同步输入框的数据的。
* 也就是说：一旦有数据发生改变，对应的data中的数据就会自动发生改变。
* lazy修饰符可以让数据在<u>失去焦点或者回车时</u>更新；

## number修饰符

`v-model.number="age"`

* 默认情况下，在输入框中无论我们输入的是字母还是数字，都会**被当做字符串类型**进行处理（自动转换为string类型）。
* 但是如果我们希望处理的是数字类型，那么最好直接将内容<u>当作数字处理</u>。
* number修饰符可以让在输入框中输入的内容自动转成数字类型；

## trim修饰符

`v-model.trim="name"`

* 如果输入的内容首尾有很多空格，通常我们希望将其去除。
* trim修饰符可以<u>过滤内容左右两边的空格</u>；
