---
{"dg-publish":true,"permalink":"/programming/font-end/framework/vue/"}
---


# 1. Vue 的基本使用

## 前置知识

### Vue 的基本认识

+ 官网
  + 英文官网: <https://vuejs.org/>
  + 中文官网: <https://cn.vuejs.org/>
+ 介绍描述
  + 渐进式 JavaScript 框架
  + 作者: 尤雨溪 (一位华裔前 Google 工程师)
  + 作用: 动态构建用户界面
+ Vue 的特点
  + 遵循 MVVM 模式
  + 编码简洁, 体积小, 运行效率高, 适合移动/PC 端开发
  + 它本身只关注 UI, 可以轻松引入 vue 插件（依赖于 vue）或其它第三库（不依赖于 vue）开发项目
+ MVC 模式（Model-view-Controller）的核心思想是有效地组合“视图”、“模型”和“控制器”，是通过三个不同部分构造一个软件或组件的理想办法。
  + 模型：用于存储数据的对象；
  + 视图：为模型提供数据显示的对象；
  + 控制器：负责具体的业务逻辑操作，即控制器根据视图提出的要求对数据做出处理，并将有关结果存储到模型中，同时负责让模型和视图进行必要的交互，当模型中的数据变化时，让视图更新显示。
+ 与其它前端 JS 框架的关联
  + 借鉴 angular 的模板和数据绑定技术
  + 借鉴 react 的组件化和虚拟 DOM 技术
+ Vue 扩展插件
  + vue-cli: vue 脚手架
  + vue-resource(axios): ajax 请求
  + vue-router: 路由
  + vuex: 状态管理
  + vue-lazyload: 图片懒加载
  + vue-scroller: 页面滑动相关
  + mint-ui: 基于 vue 的 UI 组件库 (移动端)
  + element-ui: 基于 vue 的 UI 组件库 (PC 端)

### 例子

+ 引入 Vue.js
+ 创建 Vue 对象
+ el : 指定根 element(选择器)
+ data : 初始化数据 (页面可以访问)
+ 数据绑定 : v-model
+ 显示数据 : {{xxx}}

### 理解 Vue 的 Mvvm 实现

+ ![1555758369962](/img/user/programming/font-end/framework/vue/1555758369962.png)
+ m model 数据对象（data）
  + 指令
  + 花括号表达式
+ v view UI 模板页面
+ vm 视图模型 创建的 vue 实例 vm
  + DOM 监听：
  + 数据绑定
  + vm 实例对象内部的回调函数里 this 就是 vm 实例对象，通过 this.属性名可以访问在 vm 实例对象的所有属性（data）
  + vm 实例的方法以 $ 开头

  ```html
  <div id="test">
    <input type="text" v-model="msg"><br><!--指令-->
    <input type="text" v-model="msg"><!--指令-->//模块名相同互相改变
    <p>hello {{msg}}</p><!--大括号表达式-->
  </div>
  ```

  ```js
  const vm = new Vue({ // 配置对象 options
    // 配置选项(option)
    el: '#test',  // element: 指定用vue来管理页面中的哪个标签区域
    data: { //mvvm里的model，即是数据
      msg: 'atguigu'
    }
  })
  ```

**实现的过程**

+ 监听输入框（VM）
+ 变动出发回调函数，把内容保存到 vue 的“内存”里，（M：data）

  ![1555758547067](/img/user/programming/font-end/framework/vue/1555758547067.png)

+ 把内存的内容输出到 view（VM） （V：页面）

## 模板语法

### 模板的理解

+ 动态的 html 页面
+ 包含了一些 JS 语法代码
  + 双大括号表达式
  + 指令 (以 v- 开头的自定义标签属性)
+ 双大括号表达式
  + 语法: {{exp}}
  + 功能: 向页面输出数据
  + 可以调用对象的方法

  ```js
  <p>{{msg.toUpperCase()}}</p>
  <p v-html=”msg”></p>  //innerHTML
  <p v-text=”msg”></p> //innerText
  ```

### 指令一: 强制数据绑定

+ 功能: 指定**变化的**属性值
+ 使用了 vue 的数据绑定，在 html 标签内就用 js 语法
+ 完整写法: `v-bind:xxx='yyy' `
+ yyy 会作为**表达式解析执行**

  ```js
  <a v-bind:href="url">访问指定站点 2</a> //动态指定url的值
  ```

+ 简洁写法 ` :xxx='yyy'`

  ```js
  <a :href="url">访问指定站点 2</a> //动态指定url的值
  ```

### 指令二: 绑定事件监听

+ 功能: 绑定指定事件名的回调函数
+ 完整写法:

  ```js
  v-on:事件名='回调函数'
  v-on:keyup='xxx(参数)' //可以向xxx方法传递参数
  v-on:keyup.enter='xxx'
  ```

+ 简洁写法:

  ```js
  @keyup='xxx'
  @keyup.enter='xxx'
  ```

  ```js
  methods: { //对应的回调函数卸载methods里面
    handleClick () { //es6简写语法
      alert('处理点击')
    }
  }
  ```

## 计算属性和监视属性

+ 模板内的表达式非常便利，但是设计它们的初衷是用于简单运算的。在模板中放入太多的逻辑会让模板过重且难以维护。

  ```js
  {{ message.split('').reverse().join('') }}
  ```

+ 对于任何复杂逻辑，都应当使用**计算属性**

### 计算属性

+ 在 **computed** **属性对象**中定义**计算属性**的方法
+ 计算属性的方法将直接作用于属性的 getter 函数

  ```js
  computed: {
  //需要显示数据时会执行，初始化会执行，当相关属性变化时也会执行，因为负责显示数据的是一个表达式{{}}，改变了一个变量的值，表达式的值也会改变，就是这么简单，实现了绑定
  fullName () { //计算属性的一个方法，一定要有return作为属性值
  return this.firstName + " " + this.lastName
  },
  ```

+ 计算属性可以像普通属性一样绑定，在页面中使用{{方法名}}来显示计算的结果
+ 或者与 v-model 绑定

  ```js
  <input type="text" v-model="firstName"><br>
  ```

  + input 初始化显示的就是 firstName 的值，所以改变输入框的内容相当于是改变了 firstName 的值
+ 把变量定义成方法可以实现同样的功能，但是计算属性存在缓存, 多次读取只执行一次 getter 计算

### 监视属性

+ 通过 vm 对象的 $watch() 或 watch 配置来监视指定的属性
+ 在数据变化时执行异步或开销较大的操作时，这个方式是最有用的，而且监视属性更加通用
+ 当属性变化时, 回调函数自动调用, 在函数内部进行计算
+ 回调函数自动传入两个参数，新值、旧值，用于函数内部的计算

  ```js
  watch: { //配置监视，只能监视配置的属性lastName，如果fN改变不会调用函数
  lastName: function (newVal, oldVal) {
  this.fullName2 = this.firstName + ' ' + newVal
  },
  firstName:function(){}
  }
  
  vm.$watch('lastName', function (value) {
    // 更新fullName2
    this.fullName2 = this.firstName + '-' + value
  })
  ```

+ 就这一个功能而言，计算属性比监视属性要快速，计算属性监视了需要计算的全部属性，监视属性只能监视特定的属性，这两种方法都是单向的

### 深度监视

### 计算属性高级

+ 通过 getter/setter 实现对属性数据的显示和监视
+ 其实就是 vue 计算属性的实现

  ```js
  fullName3: {
      // 当获取当前属性值时自动调用, 将返回值(根据相关的其它属性数据)作为属性值
      get () {
        console.log('fullName3 get()')
        return this.firstName + '-' + this.lastName
      },
      // 当属性值发生了改变时自动调用, 监视当前属性值变化, 同步更新相关的其它属性值
      set (value) {// fullName3的最新value值  A-B23
        console.log('fullName3 set()', value)
        // 更新firstName和lastName
        const names = value.split('-')
        this.firstName = names[0]
        this.lastName = names[1]
      }
    }
  },
  ```

## Class 与 Style 绑定

### **理解**

+ 在应用界面中, 某个 (些) 元素的样式是变化的
+ class/style 绑定就是专门用来实现动态样式效果的技术
+ `v-bind:xxx='yyy' `

### **class 绑定**

+ :class='xxx'
+ 表达式是字符串: 'classA'
  + 单个 class
+ 表达式是对象: {classA:isA, classB: isB}
  + 多个 class，通过 true or false 控制是否应用
  + 通过！可以实现单个按钮切换
  + **当确定类名，不清楚标签是否有该类名的时候时候使用对象语法**
+ 表达式是数组: ['classA', 'classB']
  + 多个 class
+ **当两个类名之间切换的时候使用三目运算符**
  + `:class=" showPwd?'on':'off' "`

### Style 绑定

+ ```js
  :style="{ color: activeColor, fontSize: fontSize +’px’ }"
  ```
+ 其中 activeColor/fontSize 是 data 属性
+ 因为是 js 的语法，所以 data 属性不能带 px，需要在 html 内加上，或者直接传入字符串
+ CSS 属性名可以用驼峰式 (camelCase) 或短横线分隔 (kebab-case，记得用单引号括起来)

### 与计算属性混合使用

+ ```html
  <div v-bind:class="classObject"></div>
  ```

  ```html
  data: {
    isActive: true,
    error: null
  },
  computed: {
    classObject: function () {
      return {
        'active': this.isActive && !this.error,
        'text-danger': this.error && this.error.type === 'fatal'
      }
    }
  }
  ```

## 条件渲染指令

### V-if 与 V-else

+ v-else 元素必须紧跟在带 v-if 或者 v-else-if 的元素的后面，否则它将不会被识别。
+ 类似于 v-else，v-else-if 也必须紧跟在带 v-if 或者 v-else-if 的元素之后。

### V-show

+ ```js
  <h1 v-show="ok">Hello!</h1>
  <h1 v-show="!ok">world!</h1>
  ```
+ 不同的是带有 v-show 的元素始终会被渲染并保留**在 DOM 中**。v-show 只是简单地切换元素的 **CSS** **属性 display，设置为 none，不设置为 none。

  > 注意：v-show 不支持 <template> 元素，也不支持 v-else。

### 比较

+ v-if 是“真正”的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被**销毁和重建**。
+ v-if 也是**惰性的**：如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块。
+ 相比之下，v-show 就简单得多——不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 进行切换。
+ 一般来说，v-if 有更高的**切换开销**，而 v-show 有更高的**初始渲染开销**。因此：
  + 如果需要非常频繁地切换，则使用 v-show 较好
  + 如果在运行时条件很少改变，则使用 v-if 较好。

## 列表渲染

### 数组: V-for / Index

+ 把一个 JS 数组渲染为 html 的一组元素

  ```html
  <ul id="example-2">
    <li v-for="(item, index) in items">
      {{ parentMessage }} - {{ index }} - {{ item.message }}
    </li>
  </ul>
  ```

  ```js
  var example2 = new Vue({
    el: '#example-2',
    data: {
      parentMessage: 'Parent',
      items: [
        { message: 'Foo' },
        { message: 'Bar' }
      ]
    }
  })
  ```

+ 也可以用 **of** 替代 in 作为分隔符，因为它是最接近 JavaScript 迭代器的语法：

  ```html
  <div v-for="item of items"></div>
  ```

### **对象: V-for / key**

+ 可以用 v-for 通过一个对象的属性来迭代。

  ```html
  <div v-for="(value, key, index) in object">
    {{ index }}. {{ key }}: {{ value }}
  </div>
  ```

  ```js
  new Vue({
    el: '#v-for-object',
    data: {
      object: {
        firstName: 'John',
        lastName: 'Doe',
        age: 30
      }
    }
  })
  ```

+ 在遍历对象时，是按 Object.keys() 的结果遍历，但是不能保证它的结果在不同的 JavaScript 引擎下是一致的。

### **key**

- 为了给 Vue 一个提示，以便它能跟踪每个节点的身份，从而重用和重新排序现有元素，你需要为每项提供一个唯一 key 属性。理想的 key 值是每项都有的唯一 id。它的工作方式类似于一个属性，所以你需要用 v-bind 来绑定动态值 (在这里使用简写)：

  ```html
  <div v-for="item in items" :key="item.id">
      <!-- 内容 -->
  </div>
  ```

- 建议尽可能在使用 v-for 时提供 key，除非遍历输出的 DOM 内容非常简单，或者是刻意依赖默认行为以获取性能上的提升。
- 因为它是 Vue 识别节点的一个通用机制，key 并不与 v-for 特别关联，key 还具有其他用途，我们将在后面的指南中看到其他用途。

### 列表的更新显示

+ 变异方法：对原生的方法进行了重写，本质上：调用原生的，再更新界面

  ```js
  this.persons[index] = newP //页面不会更新，newP不会显示
  // vue本身只监视了persons的改变，没有监视数组内部的数据变化
  // vue根本就不知道内部发生了而变化，所以这样无法更新页面
  this.persons.splice(index, 1, newP) //调用重写的方法即可
  ```

+ push()
+ pop()
+ shift()
+ unshift()
+ splice()
+ sort()
+ reverse()
+ 变异方法 (mutation method)，顾名思义，**会改变被这些方法调用的原始数组**。相比之下，也有非变异 (non-mutating method) 方法，例如：filter(), concat() 和 slice() 。这些不会改变原始数组，但**总是返回一个新数组**。当使用非变异方法时，可以用新数组替换旧数组：

  ```js
  example1.items = example1.items.filter(function (item) {
    return item.message.match(/Foo/)
  })
  ```

+ 由于 JavaScript 的限制，Vue 不能检测以下变动的数组：
  + 当利用索引直接设置一个项时，例如：vm.items[indexOfItem] = newValue
  + 当修改数组的长度时，例如：vm.items.length = newLength
  + 为了解决这些问题，要善用 splice() 方法

  ```js
  vm.items.splice(indexOfItem, 1, newValue)
  vm.items.splice(newLength)
  ```

+ 对象也有类似的问题再了解

### 列表的高级过滤、排序

+ 我们想要显示一个数组的过滤或排序副本，而不实际改变或重置原始数据。在这种情况下，可以创建返回过滤或排序数组的计算属性。
+ 列表过滤
+ 列表排序

  ```js
  <li v-for="n in evenNumbers">{{ n }}</li>
  data: {
    numbers: [ 1, 2, 3, 4, 5 ]
  },
  computed: {
    evenNumbers: function () {
      return this.numbers.filter(function (number) {
        return number % 2 === 0
      })
    }
  }
  ```

### V-for 其他用法

+ ```html
  <div>
    <span v-for="n in 10">{{ n }} </span>
  </div>
  
  ```
+ 当它们处于同一节点，v-for 的优先级比 v-if 更高，这意味着 v-if 将分别重复运行于每个 v-for 循环中。
+ 你想为仅有的一些项渲染节点时，这种优先级的机制会十分有用
+ 而如果你的目的是有条件地跳过循环的执行，那么可以将 v-if 置于外层元素 (或 <template>) 上。

## 事件处理

### **绑定监听**

+ v-on:xxx="fun"
+ @xxx="fun"
+ @xxx="fun(参数)"
+ 默认事件形参: event
+ 如果不传参数，可以直接访问 event，如果传了用 $event
+ 隐含属性对象: $event

  ```js
  <button @click="test3('abcd', $event)">test3</button>
  ```

### 事件修饰符

+ `.prevent` : 阻止事件的默认行为 event.preventDefault()
+ `.stop` : 停止事件冒泡 event.stopPropagation() 兼容性问题

  ```js
  <div style="width: 100px;height: 100px;background: blue" @click.stop="test6"></div>
  ```

+ 修饰符可以串联
+ 使用修饰符时，顺序很重要；相应的代码会以同样的顺序产生。因此，用 **v-on:click.prevent.self** 会阻止所有的点击，而 **v-on:click.self.prevent** 只会阻止对元素自身的点击。
+ `.once`：点击事件将只会触发一次
+ 不像其它只能对原生的 DOM 事件起作用的修饰符，.once 修饰符还能被用到自定义的组件事件上。

### 绑定多个事件

+ ```js
  @click="goto('/profile');toTop()"
  ```

### 按键修饰符

+ `.keycode `: 操作的是某个 keycode 值的键

  ```js
  <input type="text" @keyup.13="test7">
  //if（event.keycode===13）{}
  ```

+ .keyName : 操作的某个按键名的键 (少部分)

  ```js
  <input type="text" @keyup.enter="test7">
  ```

+ .enter
+ .tab
+ .delete (捕获“**删除**”和“**退格**”键)
+ .esc
+ .space
+ .up
+ .down
+ .left
+ .right
+ 可以通过全局 config.keyCodes 对象自定义按键修饰符别名：

  ```js
  // 可以使用 `v-on:keyup.f1`
  Vue.config.keyCodes.f1 = 112
  ```

### **系统修饰键**

+ .ctrl
+ .alt
+ .shift
+ 请注意修饰键与常规按键不同，在和 keyup 事件一起用时，事件触发时修饰键必须处于按下状态。换句话说，只有在按住 ctrl 的情况下释放其它按键，才能触发 keyup.ctrl。而单单释放 ctrl 也不会触发事件。
+ 如果你想要这样的行为，请为 ctrl 换用 keyCode：keyup.17。

### 鼠标按钮修饰符

+ .left
+ .right
+ .middle

### 为什么在 HTML 中监听事件

+ 你可能注意到这种事件监听的方式违背了关注点分离 (separation of concern) 这个长期以来的优良传统。
+ 但不必担心，因为所有的 Vue.js 事件处理方法和表达式都严格绑定在当前视图的 ViewModel 上，它不会导致任何维护上的困难。实际上，使用 v-on 有几个好处：
  + 扫一眼 HTML 模板便能轻松定位在 JavaScript 代码里对应的方法。
  + 因为你无须在 JavaScript 里手动绑定事件，你的 ViewModel 代码可以是非常纯粹的逻辑，和 DOM 完全解耦，更易于测试。
  + 当一个 ViewModel 被销毁时，所有的事件处理器都会自动被删除。你无须担心如何清理它们。

### 常用事件

+ @blur 失去焦点
+ @focus 获得焦点

### 表单输入绑定

### **使用 V-model 对表单数据自动收集**

+ 你可以用 v-model 指令在表单 <input>、<textarea> 及 <select> 元素上创建双向数据绑定。它会根据控件类型自动选取正确的方法来更新元素。尽管有些神奇，但 v-model 本质上不过是语法糖。它负责监听用户的输入事件以更新数据，并对一些极端场景进行一些特殊处理。
+ v-model 会忽略所有表单元素的 value、checked、selected 特性的初始值而总是将 Vue 实例的数据作为数据来源。你应该通过 JavaScript 在组件的 data 选项中声明初始值。
+ 对于需要使用输入法 (如中文、日文、韩文等) 的语言，你会发现 v-model 不会在输入法组合文字过程中得到更新。如果你也想处理这个过程，请使用 input 事件。
+ text/textarea
+ checkbox
+ radio
+ select //多选时绑定到一个数组（multiple 属性）
  + 可以用 v-for 渲染的动态选项

### 值绑定

+ 对于单选按钮，复选框及选择框的选项，v-model 绑定的值通常是静态字符串 (对于复选框也可以是布尔值)：

  ```html
  <!-- 当选中时，`picked` 为字符串 "a" -->
  <input type="radio" v-model="picked" value="a">
  
  <!-- `toggle` 为 true 或 false -->
  <input type="checkbox" v-model="toggle">
  
  <!-- 当选中第一个选项时，`selected` 为字符串 "abc" -->
  <select v-model="selected">
    <option value="abc">ABC</option>
  </select>
  ```

+ 但是有时我们可能想把值绑定到 Vue 实例的一个动态属性上，这时可以用 v-bind 实现，并且这个属性的值可以不是字符串。

  ```html
  <select v-model="cityId">
    <option value="">未选择</option>
    <option :value="city.id" v-for="(city, index) in allCitys" :key="city.id">{{city.name}}</option>
  </select>
  ```

#### **修饰符**

+ .number
+ 如果想自动将用户的输入值转为数值类型，可以给 v-model 添加 number 修饰符：

  ```js
  <input v-model.number="age" type="number">
  ```

+ .trim
+ 如果要自动过滤用户输入的首尾空白字符，可以给 v-model 添加 trim 修饰符：

  ```js
  <input v-model.trim="msg">
  ```

## Vue 实例的生命周期

+ 生命周期的回调函数：钩子函数
+ 不要在选项属性或回调上使用箭头函数，比如 created: () => console.log(this.a) 或 vm.$watch('a', newValue => this.myMethod())。因为箭头函数是和父级上下文绑定在一起的，this 不会是如你所预期的 Vue 实例，经常导致 Uncaught TypeError: Cannot read property of undefined 或 Uncaught TypeError: this.myMethod is not a function 之类的错误。
+ vm 对象与回调函数与 this，箭头函数定义钩子函数的匿名回调函数（定时器），箭头函数的外层是 vm 对象，可以顺利访问 this
+ 内存泄露，需要清除定时器，destory 之后 vm 失去了对页面的控制权，定时器仍在运作，beforeDestory() 清除定时器

  ![1555765511341](/img/user/programming/font-end/framework/vue/1555765511341.png)

+ 初始化（一次）- 更新（N 次）- 死亡（一次）
+ 常用的：
  + mounted ：mounted 挂载，在初始化完成后执行，发送 ajax 请求，启动定时器等异步任务
  + beforeDestory()：做收尾工作，停止定时器等
  + [Vue.nextTick( [callback, context\] )](https://cn.vuejs.org/v2/api/#Vue-nextTick)
  + 用法**：**
    + 在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM。

## 过渡&动画

### Vue 动画的理解

+ 操作 css 的 trasition 或 animation
+ vue 会给目标元素添加/移除特定的 class

### 过渡的相关类名

+ xxx-enter-active: 指定显示过程的 transition
+ xxx-leave-active: 指定隐藏过程的 transition
+ xxx-enter/xxx-leave-to: 指定隐藏时的样式
+ xxx-leave/xxx-enter-to: 指定显示时的样式

  ![1555765601979](/img/user/programming/font-end/framework/vue/1555765601979.png)

### 基本过渡动画的编码

+ 在目标元素外包裹<transition name="xxx">
+ 定义 class 样式
+ 指定过渡样式: transition
+ 指定隐藏时的样式: opacity/其它
+ 对于这些在过渡中切换的类名来说，如果你使用一个没有名字的 <transition>，则 v- 是这些类名的默认前缀。如果你使用了 <transition name="my-transition">，那么 v-enter 会替换为 my-transition-enter。

  ```html
  <style>
    /*指定过渡样式*/
    .xxx-enter-active, .xxx-leave-active {
      transition: opacity 1s
    }
    /*指定隐藏时的样式*/
    .xxx-enter, .xxx-leave-to {
      opacity: 0;
    }
  </style>
  ```

## 过滤器

### 理解过滤器

+ 功能: 对要显示的数据进行特定格式化后再显示
+ 注意: 并没有改变原本的数据, 而是产生新的对应的数据

### 定义过滤器

+ ```js
  Vue.filter(filterName, function(value[,arg1,arg2,...]){
  // 进行一定的数据处理
  return newValue
  })
  ```

### 使用过滤器

+ ```html
  <div>{{myData| filterName}}</div>
  <div>{{myData |filterName(arg)}}</div>
  <div v-bind:id="rawId | formatId"></div>
  ```
+ 过滤器可以串联：

  ```js
  {{ message | filterA | filterB }}
  ```

+ 过滤器是 JavaScript 函数，因此可以接收参数：

  ```js
  {{ message | filterA('arg1', arg2) }}
  ```

+ 这里，filterA 被定义为接收三个参数的过滤器函数。其中 message 的值作为第一个参数，普通字符串 'arg1' 作为第二个参数，表达式 arg2 的值作为第三个参数。

## 指令

+ 指令 (Directives) 是带有 v- 前缀的特殊特性。指令特性的值预期是单个 JavaScript 表达式 (v-for 是例外情况，稍后我们再讨论)。指令的职责是，当表达式的值改变时，将其产生的连带影响，响应式地作用于 DOM。回顾我们在介绍中看到的例子：

### 常用内置指令

+ v-text : 更新元素的 textContent 与 innterText 的区别？

  > <https://www.cnblogs.com/lee90/p/5715838.html>

+ v-html : 更新元素的 innerHTML
+ v-if : 如果为 true, 当前标签才会输出到页面
+ v-else: 如果为 false, 当前标签才会输出到页面
+ v-show : 通过控制 display 样式来控制显示/隐藏
+ v-for : 遍历数组/对象
+ v-on : 绑定事件监听, 一般简写为@
+ v-bind : 强制绑定解析表达式, 可以省略 v-bind
+ v-model : 双向数据绑定
+ ref : 指定唯一标识, vue 对象通过 $els 属性访问这个元素对象
  + 不是 v- 开头

  ```js
  this.$refs.msg.innerHTML
  ```

+ v-cloak : 防止闪现, 与 css 配合: [v-cloak] { display: none }

  ```html
  <style>
  [v-cloak] { display: none } //属性选择器
  </style>
  ```

  ```js
  <p v-cloak>{{content}}</p>
  ```

### 自定义指令

#### 注册全局指令

+ el: 指令属性所在的标签对象
+ binding: 包含指令相关信息的对象

  ![1555765922011](/img/user/programming/font-end/framework/vue/1555765922011.png)

  ```js
  Vue.directive('my-directive-name', function(el, binding){
  el.innerHTML = binding.value.toUpperCase()
  })
  ```

#### 注册局部指令

+ 以配置对象的形式

  ```js
  directives : {
    'my-directive-name' : { //因为有-，特殊字符，需要显示属性名’ ’
      bind :function(el, binding) {
        el.innerHTML = binding.value.toupperCase()
      }
    }
  }
  ```

  ```js
  v-my-directive-name='xxx'
  ```

### 自定义插件

+ JS

  ```js
  (function (window) {
    const MyPlugin = {}
    MyPlugin.install = function (Vue, options) {
      // 1. 添加全局方法或属性
      Vue.myGlobalMethod = function () {
        console.log('Vue函数对象的myGlobalMethod()')
      }
  
      // 2. 添加全局资源
      Vue.directive('my-directive',function (el, binding) {
        el.textContent = 'my-directive----'+binding.value
      })
  
      // 4. 添加实例方法
      Vue.prototype.$myMethod = function () {
        console.log('vm $myMethod()')
      }
    }
    window.MyPlugin = MyPlugin
  })(window)
  ```

+ HTML

  ```js
  // 声明使用插件(安装插件: 调用插件的install())
  Vue.use(MyPlugin) // 内部会调用插件对象的install()
  
  const vm = new Vue({
    el: '#test',
    data: {
      msg: 'HaHa'
    }
  })
  Vue.myGlobalMethod()
  vm.$myMethod()
  ```

## Vue 实例的常用方法

### nextTick( [callback, context] )

+ 在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM

  ```js
  watch:{
    categorys(value){ //当categorys发生改变，说明categorys有数据了，
      //创建一个swiper实例对象，实现轮播
      /*setTimeout(()=>{
        new Swiper('.swiper-container', {
          loop: true, // 循环模式选项
          // 如果需要分页器
          pagination: {
            el: '.swiper-pagination',
          },
        })
      },100)*/
  
      //界面更新立即创建swiper实例
      this.$nextTick(()=>{
        new Swiper('.swiper-container', {
          loop: true, // 循环模式选项
          // 如果需要分页器
          pagination: {
            el: '.swiper-pagination',
          },
        })
      }) //一旦完成界面更新会立即调用，此条语句应该写在更新数据之后
    }
  }
  ```

# 2. Vue 组件化编码

## 使用 Vue-cli 创建模板项目

### 说明

+ vue-cli 是 vue 官方提供的脚手架工具
+ github: https://github.com/vuejs/vue-cli
+ 作用: 从 https://github.com/vuejs-templates 下载**模板项目**

### 创建 Vue 项目

+ ```js
  npm install -g vue-cli
  vue init webpack vue_demo //使用的模板名，其他可用模板如：webpack-simple broswerify pwa等， vue_demo是项目名
  cd vue_demo
  npm install  //安装依赖
  npm run dev //开发环境，在内存中打包好
  访问: http://localhost:8080/
  ```

### **模板项目的结构**

+ ```js
  |-- build : webpack 相关的配置文件夹(基本不需要修改)
  		|-- dev-server.js : 通过 express 启动后台服务器
  		|-- webpack.base.conf.js 主要配置文件，可以 配置入口文件等
  |-- config: webpack 相关的配置文件夹(基本不需要修改)
  		|-- index.js: 指定的后台服务的端口号和静态资源文件夹//还可以指定是否自动打开浏览器
  |-- node_modules
  |-- src : 源码文件夹
  		|-- components: vue 组件及其相关资源文件夹
  		|-- App.vue: 应用根主组件
  		|-- main.js: 应用入口 js
  |-- static: 静态资源文件夹
  |-- .babelrc: babel 的配置文件 //rc runtie control 描述运行时控制
  |-- .eslintignore: eslint 检查忽略的配置
  |-- .eslintrc.js: eslint 检查的配置
  |-- .gitignore: git 版本管制忽略的配置
  |-- index.html: 主页面文件
  |-- package.json: 应用包配置文件
  |-- README.md: 应用描述说明的 readme 文件
  ```
+ main.js

  ```js
  import Vue from 'vue' //创建vue实例，需要先引入vue
  import App from './App' //引入根组件
  
  Vue.config.productionTip = false
  
  new Vue({
    el: '#app',
    components: { App }, 
  //相当于App:App，将引入的组件映射成App
    /*组件：一个局部功能模块 html css js*/
    template: '<App/>' //定义组件标签,根组件会自动挂载到页面上
  })
  ```

+ App.vue

  ```vue
  <template…> //模板必须要有一个根标签
    <HelloWorld/> //使用组件标签
  </template>
  <script…> 
  import HelloWorld from './components/HelloWorld'
  export default {
    name: 'App',
    components: { //映射组件标签
      HelloWorld
    }
  }
  </script> 
  <style…>
  ```

+ HelloWorld.vue

  ```vue
  <template…> //模板必须要有一个根标签
    <script…> 
    export default { //配置对象，与vm实例一致
  data (){ //组件里的data只能是函数，不可以是对象
    return{ //返回一个对象即可
      msg:
    }
    } 
  }
  <style…>
  ```

## 组件的定义与使用

+ 模板对象

  ```html
  <template>
  //页面模板
  </template>
  ```

+ js 模块对象

  ```vue
  &lt;script&gt;
  export default {
  data() {return {}},
  methods: {},
  computed: {},
  components: {}
  }
  &lt;/script&gt;
  
  ```

+ 样式

  ```vue
  <style>
  样式定义
  </style>
  ```

+ 基本使用
  + 引入组件
  + 映射成标签
  + 使用组件标签
+ 关于标签名与标签属性名书写问题
  + 写法一: 一模一样
  + 写法二: 大写变小写, 并用 - 连接

## 组件间通信

+ 组件间通信基本原则
  + 不要在子组件中直接修改父组件的状态数据
  + 数据在哪, 更新数据的行为 (函数) 就应该定义在哪
+ vue 组件间通信方式
  + props
  + vue 的自定义事件
  + 消息订阅与发布 (如: pubsub 库)
  + slot
  + vuex(后面单独讲)

### 组件间通信 : Props

+ 使用组件标签时
+ 传递普通字符串，直接名值对
+ 传递表达式、数字等要绑定数据

  ```js
  <my-component name='tom' :age='3' :set-name='setName'></my-component>
  ```

+ 定义 MyComponent 时
  + 在组件内声明所有的 props
+ 方式一: 只指定名称

  ```js
  props: ['name', 'age', 'setName']
  ```

+ 方式二: 指定名称和类型

  ```js
  props: {
  	name: String,
  	age: Number,
  	setNmae: Function
  }
  ```

+ 方式三: 指定名称/类型/必要性/默认值

  ```js
  props: {
  	name: {type: String, required: true, default:xxx},
  }
  ```

**注意**

+ 此方式用于父组件向子组件传递数据
+ 所有标签属性都会成为组件对象的属性, 模板页面可以直接引用

**问题:**

+ 如果需要向非子后代传递数据必须多层逐层传递
+ 兄弟组件间也不能直接 props 通信, 必须借助父组件才可以

### 组件间通信：vue 自定义事件

+ 绑定事件监听

  ```js
  // 方式一: 通过 v-on 绑定
  @delete_todo="deleteTodo" //绑定监听一般用这种
  // 方式二: 通过$on()
  this.$refs.xxx.$on('delete_todo', function (todo) {
  	this.deleteTodo(todo) //xxx为对应的标签元素的ref属性
  })
  ```

+ 触发事件

  ```js
  // 触发事件(只能在父组件中接收)
  this.$emit(eventName, data)
  ```

**注意:**

+ 此方式只用于**子**组件向**父**组件发送消息 (数据)
+ 用于传递函数比较合适，仅父子间，隔代不可
+ 问题: 隔代组件或兄弟组件间通信此种方式不合适

### 组件间通信 3: 消息订阅与发布 (PubSubJS 库)

+ 安装 `npm i pubsub-js -g --save`
+ 订阅消息 `PubSub.subscribe('msg', function(msg, data){})`
+ 发布消息 `PubSub.publish('msg', data)`

**注意**

+ 优点: 此方式可实现任意关系组件间通信 (数据)
+ 事件的 2 个重要操作 (总结)
  + 绑定事件监听 (订阅消息)
    + 目标: 标签元素 <button>
    + 事件名 (类型): click/focus
    + 回调函数: function(event){}
  + 触发事件 (发布消息)
    + DOM 事件: 用户在浏览器上对应的界面上做对应的操作
    + 自定义: 编码手动触发

### 组件间通信 4: Slot

+ 理解
  + 此方式用于父组件向子组件传递**标签数据**
  + 当组件标签反复使用的时候适合使用 slot
+ slot 标签
  + 通过 name 属性标识
  + 可以接受标签数据
  + 相关的标签属性的运算在父组件进行，如：绑定事件、计算属性等，需要把相关代码移到父组件内
+ 子组件: Child.vue

  ```vue
  <template>
  <div>
    <slot name="xxx">不确定的标签结构 1</slot>
    	<div>组件确定的标签结构</div>
    <slot name="yyy">不确定的标签结构 2</slot>
  </div>
  </template>
  ```

+ 父组件: Parent.vue

  ```vue
  <child>
  	<div slot="xxx">xxx 对应的标签结构</div>
  	<div slot="yyy">yyyy 对应的标签结构</div>
  </child>
  ```

+ 与普通的组件标签不同，需要写成标签体的形式，而不是自闭合标签，为的是把需要传递的 slot 放在组件标签内

# 3. Vue UI 组件库

## Mint UI:

+ <http://mint-ui.github.io/#!/zh-cn>
+ 说明: 饿了么开源的基于 vue 的移动端 UI 组件库
+ 下载: `npm install --save mint-ui`
+ 实现按需打包

  ```
  // 1. 下载
  npm install --save-dev babel-plugin-component
  // 2. 修改 babel 配置
  "plugins": ["transform-runtime",["component", [
  {
  "libraryName": "mint-ui",
  "style": true
  }
  ]]]
  ```

+ mint-ui 组件分类
  + 标签组件
  + 非标签组件

## Elment

+ <http://element-cn.eleme.io/#/zh-CN>
+ 说明: 饿了么开源的基于 vue 的 PC 端 UI 组件库

# 4. Vue-router

## 理解

+ 本质上是键值对（映射关系），key 是 path， 后台 value 是处理请求的回调函数，前台路由 value 是组件
+ 点击路由连接本身并不向后台发请求，而是路由组件发送请求

## 说明

+ 官方提供的用来实现 SPA 的 vue 插件
+ github: <https://github.com/vuejs/vue-router>
+ 中文文档: <http://router.vuejs.org/zh-cn/>
+ 下载:` npm install vue-router –save`

# API

## Vue-router 的理解和使用

+ router-view/router-link/keep-alive
+ $router: 路由器对象, 包含一些操作路由的功能函数, 来实现编程式导航 (跳转路由)
+ $route: 当前路由对象, 一些当前路由信息数据的容器, path/meta/query/params

## VueRouter()

+ router/index.js
+ 用于创建路由器的构建函数

  ```js
  new VueRouter({
  // 多个配置项
  })
  ```

## 路由配置

+ router/index.js
+ ```js
  routes: [
    { // 一般路由
      path: '/about',
      component: About
    },
    { // 自动跳转路由 配置默认路由
      path: '/',
      redirect: '/about'
    }
  ]
  ```

## 注册路由器

+ main.js
+ ```js
  import router  from './router'
  new Vue({
    router //配置对象的属性名都是一些固定的属性名，不可以随意修改
  })
  ```

## 使用路由组件标签

+ <router-link>: 用来生成路由链接
+ <router-view>: 用来显示当前路由组件界面

  ```vue
  <router-link to="/xxx">Go to XXX</router-link>
  <router-view></router-view>
  ```

## 基本路由

+ 路由组件放在 views/pages
+ 非路由组件放在 components
+ 创建路由器需要专门定制一个模块，用于配置路由
+ 显示哪个路由 哪个路由就会有 router-link-active 类属性，可以为其配置 css
+ 优化路由器配置

  ```js
  linkActiveClass: 'active', // 指定选中的路由链接的 class
  ```

**编写使用路由的 3 步**

1. 定义路由组件
2. 注册路由
3. 使用路由

## 嵌套路由

+ 配置嵌套路由: router.js

  ```js
  path: '/home',
    component: home,
      children*: [
        {
          path: 'news', //path的 / 永远代表根路径，所以不要加/，或者写完整路径
          component: News
        },
        {
          path: 'message',
          component: Message
        },
        {path:””,redirect:’/home/news’}
      ]
  ```

## 缓存路由组件对象

**理解**

+ 默认情况下, 被切换的路由组件对象会死亡释放, 再次回来时是重新创建的
+ 如果可以缓存路由组件对象, 可以提高用户体验
+ 编码实现

  ```vue
  <keep-alive>
  <router-view></router-view>
  </keep-alive>
  ```

## 向路由组件传递数据

### 方式 1: 路由路径携带参数 (**param/query**)

+ 配置路由

  ```js
  children: [
    {
      path: 'mdetail/:id', //占位符 param参数形式 相对的query参数 ？号形式
      /*path:’mdetail’*/
      component: MessageDetail
    }
  ]
  ```

+ 路由路径
+ `/home/message/mdetail/?id=$(m.id)`

  ```vue
  <router-link :to="'/home/message/mdetail/'+m.id">{{m.title}}</router-link>
  ```

+ 路由组件中读取请求参数
+ $route 代表当前路由

  ```js
  this.$route.params.id
  ```

### 方式 2:`<router-view>` 属性携带数据

+ ```vue
  <router-view :msg="msg"></router-view>
  ```
+ 在路由内声明 props，类似于组件间的数据传递

## 编程式路由导航

+ this.$router.push(path): 相当于点击路由链接 (可以返回到当前路由界面)
+ this.$router.replace(path): 用新路由替换当前路由 (不可以返回到当前路由界面)
+ this.$router.back(): 请求 (返回) 上一个记录路由
+ this.$router.go(-1): 请求 (返回) 上一个记录路由
+ this.$router.go(1): 请求下一个记录路由

## 路由 Meta

+ route/index.js

  ```js
  {
     path: '/msite',
     component: Msite,
     meta:{
       showFooter:true
     }
   }
  ```

+ App.vue
+ <FooterGuide **v-if**="$route.meta.showFooter"/>

# 5. Vuex

## Vuex 理解

### Vuex 是什么

+ github 站点: https://github.com/vuejs/vuex
+ 在线文档: <https://vuex.vuejs.org/zh-cn/>
+ 简单来说: 对 vue 应用中多个组件的共享状态进行集中式的管理 (读/写)

### 状态自管理应用（单项数据流：view）

+ state: 驱动应用的数据源（data）
+ view: 以声明方式将 state 映射到视图（模板）
+ actions: 响应在 view 上的用户输入导致的状态变化 (包含 n 个更新状态的方法)

  ![1555809197499](/img/user/programming/font-end/framework/vue/1555809197499.png)

  ![1555809206241](/img/user/programming/font-end/framework/vue/1555809206241.png)

### 多组件共享状态的问题

+ 多个视图依赖于同一状态
+ 来自不同视图的行为需要变更同一状态
+ 以前的解决办法
  + 将**数据**以及**操作数据**的行为都定义在**父组件**
  + 将数据以及操作数据的行为**传递**给需要的各个子组件 (有可能需要多级传递)
+ **vuex** **就是用来解决这个问题的**

## Vuex 核心概念和 API

### State

+ vuex 管理的状态对象
+ 它应该是唯一的
+ 把组件中 data 的数据交给 vuex 的 state 管理

  ```js
  const state = { //初始化状态
  	xxx: initValue
  }
  ```

+ 在相应组件

  ```js
  import {mapState} from 'vuex' //引入state状态
  computed: {
    ...mapState(['userInfo']) //将state状态转化为组件的计算属性
  }
  ...mapState(['postInfo','userInfo'])
  
  this.userInfo.isVerification //在组件任意位置均可直接使用，通过this在script内调用
  ```

### Mutations

+ 包含多个直接更新 state 的方法 (回调函数) 的对象
+ 谁来触发: action 中的 commit('mutation 名称 ')
+ 只能包含同步的代码, 不能写异步代码

### Actions

+ 包含多个事件回调函数的对象
+ 通过执行: commit() 来触发 mutation 的调用, 间接更新 state
+ 谁来触发: 组件中: `this.$store.dispatch('action 名称', data1) // 'zzz'`
+ 可以包含异步代码 (定时器, ajax)

  ```js
  const actions = {
    zzz ({commit, state}, data1) {
      commit('yyy', {data1})
    }
  }
  ```

  ```js
  this.$store.dispatch('upDate', userInfo)
  ```

+ 通过 dispatch 在任意组件可以调用 action 里的函数，通过计算属性也可以将 action 转换为组件内的函数，再进行调用

### Getters

+ 包含多个计算属性 (get) 的对象
+ 谁来读取: 组件中: `this.$store.getters.xxx`

  ```js
  const getters = {
    mmm (state) {
      return ... }
  }
  ```

### Modules

+ 包含多个 module
+ 一个 module 是一个 store 的配置对象
+ 与一个组件 (包含有共享数据) 对应

### 向外暴露 Store 对象

+ store.js
+ ```js
  /*vuex 的核心管理对象 store 对象模块*/
  import Vue from 'vue'
  import Vuex from 'vuex'
  import state from './state'
  import mutations from './mutations'
  import actions from './actions'
  import getters from './getters'
  Vue.use(Vuex)
  export default new Vuex.Store({
    state,
    mutations,
    actions,
    getters
  })
  ```

### 映射 Store

+ main.js

- ```js
  import store from './store'
  new Vue({
    el: '#app',
    render: h => h(App), 
    /*
     * render:function(creaElement){
     *   return creaElement(App) //<App/>
     * }
    */
    store // 所有的组件对象都多了一个属性: $store(值就是store对象)
  })
  ```

### Store 对象

+ 所有用 vuex 管理的组件中都多了一个属性 $store, 它就是一个 store 对象
+ 属性:
  + **state:** **注册的 state** **对象**
  + getters: 注册的 getters 对象
+ 方法:
  + dispatch(actionName, data): 分发调用 action

### 组件中使用 Store

#### 方法一

+ ```js
  {{$store.state.count}}
  ```

  ```js
  computed:{
    eoo(){ 
      //组件对象的属性$store，所以需要this
      return this.$store.getters.eoo 
    }},
    methods:{
      increment(){
        this.$store.dispatch('increment') //分配对应的actions
      }
    }
  }
  ```

#### 方法二（优化）

+ ```js
  import {mapState, mapGetters, mapActions} from 'vuex'
  export default {
  computed: {
  ...mapState(['xxx']),
  ...mapGetters(['mmm']),
  /*...mapGetters({eoo:eoo2})方法对应的名字不同，把getters的eoo2赋值给eoo，然后组件照常引用eoo*/
  }
  methods: mapActions(['zzz'])
  }
  {{xxx}} {{mmm}} @click="zzz(data)"
  ```

# 6. Vue 编码习惯

## 项目源码目录设计

+ ![1555811692926](/img/user/programming/font-end/framework/vue/1555811692926.png)

## 组件化模板

+ ```vue
  <template>
  	<div>
    </div>
  </template>
  <script>
    export default {
    }
  </script>
  <style lang="stylus" rel="stylesheet/stylus">
  </style>
  ```

## Ajax 请求

+ 在 `mounted()` 发送请求

  ```js
  import {reqCategorys} from './api'
  
  export default {
    name: 'App',
    async mounted(){
      let result = await reqCategorys()
      console.log(result)
    }
  ```
