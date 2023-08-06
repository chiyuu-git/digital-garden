---
{"dg-publish":true,"permalink":"/programming/font-end/framework/framework-common/"}
---


# 前端路由

## 什么是前端路由

前端路由就是把不同路由对应不同的内容或页面的任务交给前端来做，之前是通过服务端根据 url 的不同返回不同的页面实现的。

## 什么时候使用前端路由

在单页面应用，大部分页面结构不变，只改变部分内容的使用

## 前端路由有什么优点和缺点

优点：用户体验好，不需要每次都从服务器全部获取，快速展现给用户

缺点：单页面无法记住之前滚动的位置，无法在前进，后退的时候记住滚动的位置

前端路由一共有两种实现方式，一种是通过 hash 的方式，一种是通过使用 pushState 的方式。

详细资料可以参考： [《什么是“前端路由”》](https://segmentfault.com/q/1010000005336260) [《浅谈前端路由》](https://github.com/kaola-fed/blog/issues/137) [《前端路由是什么东西？》](https://www.zhihu.com/question/53064386)

# NextTick

因为 js 是单线程的，在 setData 之后，到底什么时候派发 nextTickReach 是可以有选择的

1. data 真正更新之后马上 reach，此时框架控制的节点树都还没更新呢，别说是真实的 dom 树了
2. 等框架控制的节点树更新完之后派发，对于 san 就是 changes 传导到了每个节点的 _update 方法之后，对于 vue 和 react 就是 vDom 更新完毕之后

讨论的 issue：https://github.com/baidu/san/issues/141

vue 最新的源码：https://github.com/vuejs/vue/blob/dev/dist/vue.js

把 na 组件的插入操作放在 nextTick 中，可以延后执行 na 组件的插入操作，可以提升组件性能

> san 的视图更新是异步的。组件数据变更后，将在下一个时钟周期更新视图。如果你修改了某些数据，想要在 DOM 更新后做某些事情，则需要使用 `nextTick` 方法。
>
> san.dev.js 搜索 nextTick 即可

只能是 attached 之后了，感觉是类似 FMP 的一个逻辑，可以 san 组件实例已经走到 attached 了，再插入 NA 组件本身

用 cts 实例测试发现好几个 cover-view 都 attached 了 之后，此时很多 h5 的内容都已经上屏了，才进去第一个 nextTick

> 假如我有 1w 个 na 组件，也会在全部 attached 之后才到 nextTick 的轮次吗？感觉有可能哦
>
> 从上面的表现来看，我们这里的 nextTick 不就是宏任务的吗？不一定哦，因为 debugger 是可以挂起 js 线程的，alert 会占用 js 线程，并没有让给 gui，反而是 debugger 是让给了 gui 的
>
> 可以测试一下 nextTick 的微任务和宏任务了，无论是微任务还是宏任务，反正端能力都会异步的，除非是宏任务，可以做到保证在 h5 的组件渲染完成
>
> 我们现在 setImmediate 和 messageChannel 都是有的，所以是宏任务

san 的数据更新操作是异步的，会被打包在一个 nextTick 中。因此我们再使用 nextTick 可以获取到数据更新的 nextTick 之后的 data，微任务队列

还有一个点就是 dom 的更新和 UI 渲染是两个概念，因为 js 线程和渲染线程是互斥的，所以 UI 渲染之前，dom 就已经是更新完的了。

> 这就涉及到我们 dom api 获取的究竟是？肯定是从 dom 树入手的呀，render 树究竟是怎么样的，确实有部分 dom api 是从 render 树 入手的，但是有部分的 dom api 会强制渲染，这个时候会发生什么？

如果是 promise 这种微任务实现，当我们在 nextTick 中再次 setData，那么会在 UI 渲染前成功 setData

如果选择宏任务实现会在 UI 渲染之后，导致重新渲染，因此微任务应该是更好的选择，但是为什么 san 中优先选择了宏任务呢？vue 目前也是 promise 优先

> 我们 slave 触发的是哪个 nextTick 呢？我们组件代码运行在 slave 上，是 webview 环境，有 js 解释器 bom api dom api

我自己也是可以测试的，无论是 san 还是 vue，我需要有一个方法知道 dom 到底更新了没有，还需要明确一点 dom 更新和 UI 渲染的关系

网上的文章并没有错，nextTick 的初衷是为了获取更新后的 dom？ 那问题就只剩下上一个了，dom 的更新和 ui 的渲染，肯定是分开的吧，不可能说 UI 渲染完了 dom 才更新了，肯定是更新后的 dom 被用于 UI 渲染了

我只要整一个函数更新 dom，然后看看实际的 dom 结构更新和渲染的先后关系

> 采用 alert 语句进行提示，alert 语句会 block 住 js 线程，将执行权让给 gui 渲染线程，执行 alert 之后浏览器会把这个语句之前的所有对 dom 的操作都进行体现。
>
> 感觉打断点的话也会被 js 线程也会被吊起，ui 渲染会直接进行，因此没办法捕捉到 dom 更新了，但是 UI 没渲染的那个瞬间
>
> 体验 dom 更新了，但是 UI 没有渲染的过程：https://blog.csdn.net/qdmoment/article/details/83657410

# 数据绑定

## 单向数据绑定

单向数据绑定，带来单向数据流

指的是我们先把模板写好，然后把模板和数据（数据可能来自后台）整合到一起形成 HTML 代码，然后把这段 HTML 代码插入到文档流里面。适用于整体项目，并于追溯。

![img](/img/user/programming/font-end/framework/framework-common/4859545-cca9cea07b1e43ed.jpg)

### 优点

1. 所有状态的改变可记录、可跟踪，源头易追溯;
2. 所有数据只有一份，组件数据只有唯一的入口和出口，使得程序更直观更容易理解，有利于应用的可维护性;
3. 一旦数据变化，就去更新页面 (data- 页面)，但是没有 (页面 -data);
4. 如果用户在页面上做了变动，那么就手动收集起来 (双向是自动)，合并到原有的数据中。

### 缺点

1. HTML 代码渲染完成，无法改变，有新数据，就须把旧 HTML 代码去掉，整合新数据和模板重新渲染;
2. 代码量上升，数据流转过程变长，出现很多类似的样板代码;
3. 同时由于对应用状态独立管理的严格要求 (单一的全局 store)，在处理局部状态较多的场景时 (如用户输入交互较多的“富表单型”应用)，会显得啰嗦及繁琐。

## 双向数据绑定

双向数据绑定，带来双向数据流。**AngularJS2 添加了单向数据绑定**

数据模型（Module）和视图（View）之间的双向绑定。无论数据改变，或是用户操作，都能带来互相的变动，自动更新。适用于项目细节，如：UI 控件中 (通常是类表单操作)。

![img](/img/user/programming/font-end/framework/framework-common/4859545-3b49b67b1bfdc7c0.jpg)

### 优点

1. 用户在视图上的修改会自动同步到数据模型中去，数据模型中值的变化也会立刻同步到视图中去；
2. 无需进行和单向数据绑定的那些 CRUD（Create，Retrieve，Update，Delete）操作；
3. 在表单交互较多的场景下，会简化大量业务无关的代码。

### 缺点

1. 无法追踪局部状态的变化；
2. “暗箱操作”，增加了出错时 debug 的难度；
3. 由于组件数据变化来源入口变得可能不止一个，数据流转方向易紊乱，若再缺乏“管制”手段，血崩。

> 在 angular 中，他没有办法判断你的数据是否做了更改， 所以它设置了一些条件，当你触发了这些条件之后，它就执行一个检测来遍历所有的数据，对比你更改了地方，然后执行变化。这个检查很不科学。而且效率不高，有很多多余的地方，所以官方称为脏检查

### 本质

双向绑定 = 单向绑定 + UI 事件监听。

请看下面的代码示例

```vue
<body>
  <div id="app">
    <input type="text" v-model="meg">
    <p>{{data}}</p>
  </div>
 
  <script>
    var app = new Vue({
      el:'#app',
      data :{
        meg:''
      }
     
    })
  </script>
</body>
```

当你在页面的 input 框中输入值时，下面一行同步显示内容，如果我们不要 v-model 指令能实现这个效果吗? 只需要改为:

```js
 //首先设置value属性为meg，然后监听输入事件
<input type="text" :value="meg" @input="meg=$event.target.value"> 
```

## 具体实例

目前几种主流的 mvc(vm) 框架都实现了单向数据绑定，而我所理解的双向数据绑定无非就是在单向绑定的基础上给可输入元素（input、textare 等）添加了 change(input) 事件，来动态修改 model 和 view，并没有多高深。所以无需太过介怀是实现的单向或双向绑定。

### 发布者 - 订阅者模式

一般通过 sub, pub 的方式实现数据和视图的绑定监听，更新数据方式通常做法是 `vm.set('property', value)`，这里有篇文章讲的比较详细，有兴趣可点 [这里](http://www.html-js.com/article/Study-of-twoway-data-binding-JavaScript-talk-about-JavaScript-every-day)

这种方式现在毕竟太 low 了，我们更希望通过 `vm.property = value ` 这种方式更新数据，同时自动更新视图，于是有了下面两种方式

### 脏值检查

angular.js 是通过脏值检测的方式比对数据是否有变更，来决定是否更新视图，最简单的方式就是通过 `setInterval()` 定时轮询检测数据变动，当然 Google 不会这么 low，angular 只有在指定的事件触发时进入脏值检测，大致如下：

- DOM 事件，譬如用户输入文本，点击按钮等。( ng-click )
- XHR 响应事件 ( $http )
- 浏览器 Location 变更事件 ( $location )
- Timer 事件 ( \$timeout , ​\$interval )
- 执行 \$digest() 或 ​\$apply()

### 数据劫持

vue.js 则是采用数据劫持结合发布者 - 订阅者模式的方式，通过 `Object.defineProperty()` 来劫持各个属性的 `setter`，`getter`，在数据变动时发布消息给订阅者，触发相应的监听回调。

vue3 通过 proxy 实现劫持

# 响应式机制

[Observer](vue/mini-vue2.md#Observer)

[[programming/font-end/framework/vue/mini-vue3\|vue/mini-vue3]]

# 对比

## 与 React 对比

react 手动 setState 更新了 vm 之后，vm 的变动到 v 的更新 (vDOM) 实现了单向绑定

vue 中手动更新了 data 之后，vm 的变动到 v 的更新 (vDom) 实现了单向绑定

在 React 应用中，当某个组件的状态发生变化时，它会以该组件为根，重新渲染整个组件子树。当然，这可以通过 `shouldComponentUpdate` 这个生命周期方法来进行控制 purerender，但 Vue 将此视为默认的优化。

vue 中实现数据绑定靠的是数据劫持（Object.defineProperty()）+ 观察者模式。在 Vue 应用中，组件的依赖是在渲染过程中自动追踪的，所以系统能精确知晓哪个组件需要被重渲染。你可以理解为每一个组件都已经自动获得了 shouldComponentUpdate，并且没有上述的子树问题限制。[Vue对比其他框架](https://link.jianshu.com?t=https%3A%2F%2Fcn.vuejs.org%2Fv2%2Fguide%2Fcomparison.html)

![img](/img/user/programming/font-end/framework/framework-common/8066565-beeb0aca615e83f6-0458871.png)

![img](/img/user/programming/font-end/framework/framework-common/8066565-9cac619972512f0d-0458871.png)

## 实现双向绑定 Proxy 与 Object.defineProperty 相比优劣如何?

1. Object.definedProperty 的作用是劫持一个对象的属性，劫持属性的 getter 和 setter 方法，在对象的属性发生变化时进行特定的操作。而 Proxy 劫持的是整个对象。

   ```js
   let obj = {name: 'Yvette', hobbits: ['travel', 'reading'], info: {
       age: 20,
       job: 'engineer'
   }};
   let p = new Proxy(obj, {
       get(target, key) { //第三个参数是 proxy， 一般不使用
           console.log('读取成功');
           return Reflect.get(target, key);
       },
       set(target, key, value) {
           if(key === 'length') return true; //如果是数组长度的变化，返回。
           console.log('设置成功');
           return Reflect.set([target, key, value]);
       }
   });
   p.name = 20; //设置成功
   p.age = 20; //设置成功; 不需要事先定义此属性
   p.hobbits.push('photography'); //读取成功;注意不会触发设置成功
   p.info.age = 18; //读取成功;不会触发设置成功
   ```

2. Proxy 会返回一个代理对象，我们只需要操作新对象即可，而 `Object.defineProperty` 只能遍历对象属性直接修改。
3. Object.definedProperty 不支持数组，更准确的说是不支持数组的各种 API，因为如果仅仅考虑 arry[i] = value 这种情况，是可以劫持的，但是这种劫持意义不大。而 Proxy 可以支持数组的各种 API。

   > Object.definedProperty 可以将数组的索引作为属性进行劫持，但是仅支持直接对 arry[i] 进行操作，不支持数组的 API，非常鸡肋。
   >
   > Proxy 可以监听到数组的变化，支持各种 API。注意数组的变化触发 get 和 set 可能不止一次，如有需要，自行根据 key 值决定是否要进行处理。

   ```js
   let hobbits = ['travel', 'reading'];
   let p = new Proxy(hobbits, {
       get(target, key) {
           // if(key === 'length') return true; //如果是数组长度的变化，返回。
           console.log('读取成功');
           return Reflect.get(target, key);
       },
       set(target, key, value) {
           // if(key === 'length') return true; //如果是数组长度的变化，返回。
           console.log('设置成功');
           return Reflect.set([target, key, value]);
       }
   });
   p.splice(0,1) //触发get和set，可以被劫持
   p.push('photography');//触发get和set
   p.slice(1); //触发get；因为 slice 是不会修改原数组的
   ```

4. 尽管 Object.defineProperty 有诸多缺陷，但是其兼容性要好于 Proxy.

# Virtual DOM

我对 Virtual DOM 的理解是，

首先对我们将要插入到文档中的 DOM 树结构进行分析，使用 js 对象将其表示出来，比如一个元素对象，包含 TagName、props 和 Children 这些属性。然后我们将这个 js 对象树给保存下来，最后再将 DOM 片段插入到文档中。

当页面的状态发生改变，我们需要对页面的 DOM 的结构进行调整的时候，我们首先根据变更的状态，重新构建起一棵对象树，然后将这棵新的对象树和旧的对象树进行比较，记录下两棵树的的差异。

最后将记录的有差异的地方应用到真正的 DOM 树中去，这样视图就更新了。

我认为 Virtual DOM 这种方法对于我们需要有大量的 DOM 操作的时候，能够很好的提高我们的操作效率，通过在操作前确定需要做的最小修改，尽可能的减少 DOM 操作带来的重流和重绘的影响。其实 Virtual DOM 并不一定比我们真实的操作 DOM 要快，这种方法的目的是为了提高我们开发时的可维护性，在任意的情况下，都能保证一个尽量小的性能消耗去进行操作。

详细资料可以参考： [《Virtual DOM》](https://juejin.im/book/5bdc715fe51d454e755f75ef/section/5bdc72e6e51d45054f664dbf) [《理解 Virtual DOM》](https://github.com/y8n/blog/issues/5) [《深度剖析：如何实现一个 Virtual DOM 算法》](https://github.com/livoras/blog/issues/13) [《网上都说操作真实 DOM 慢，但测试结果却比 React 更快，为什么？》](https://www.zhihu.com/question/31809713/answer/53544875)

## 如何比较两个 DOM 树的差异

两个树的完全 diff 算法的时间复杂度为 O(n^3) ，但是在前端中，我们很少会跨层级的移动元素，所以我们只需要比较同一层级的元素进行比较，这样就可以将算法的时间复杂度降低为 O(n)。

算法首先会对新旧两棵树进行一个深度优先的遍历，这样每个节点都会有一个序号。在深度遍历的时候，每遍历到一个节点，我们就将这个节点和新的树中的节点进行比较，如果有差异，则将这个差异记录到一个对象中。

在对列表元素进行对比的时候，由于 TagName 是重复的，所以我们不能使用这个来对比。我们需要给每一个子节点加上一个 key，列表对比的时候使用 key 来进行比较，这样我们才能够复用老的 DOM 树上的节点。

## 为啥新框架都没有虚拟 DOM 了?

问问 gpt 没啥新意