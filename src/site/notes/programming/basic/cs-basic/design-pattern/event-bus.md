---
{"dg-publish":true,"permalink":"/programming/basic/cs-basic/design-pattern/event-bus/"}
---


# 观察者模式

## 概念

是软件设计模式的一种。在此种模式中，一个目标对象管理所有相依于它的观察者对象，并且在它本身的状态改变时 **主动** 发出通知。这通常透过呼叫各观察者所提供的方法来实现。此种模式通常被用来实现事件处理系统。

简单来说，观察者模式就是，一个对象（被观察者）的状态发生改变时，会通知所有依赖它的对象（观察者），这两者是直接关联的。

如图所示，当 Subject(被观察对象) 状态发生变化时，会给 **所有** 的 Observers(观察者们) 发送一个通知函数，观察者们接收到通知后通常会调用各自的更新函数。

**意图**：定义对象间的 **一对多** 的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并被自动更新。

**主要解决**：一个对象状态改变给其他对象通知的问题，而且要考虑到易用和低耦合，保证高度的协作

**何时使用**：一个对象（目标对象）的状态发生改变，所有的依赖对象（观察者对象）都将得到通知，进行广播通知。

**如何解决**：使用面向对象技术，可以将这种依赖关系弱化

**关键代码**：在抽象类里有一个 ArrayList 存放观察者们

**应用实例：**

1. 拍卖的时候，拍卖师观察最高标价，然后通知给其他竞价者竞价
2. 西游记里面悟空请求菩萨降服红孩儿，菩萨洒了一地水招来一个老乌龟，这个乌龟就是观察者，他观察菩萨洒水这个动作

**优点：**

1. 观察者和被观察者是抽象耦合的
2. 建立一套触发机制。

**缺点：**

1. 如果一个被观察者对象有很多的直接和间接的观察者的话，将所有的观察者都通知到会花费很多时间。
2. 如果在观察者和观察目标之间有循环依赖的话，观察目标会触发它们之间进行循环调用，可能导致系统崩溃。
3. 观察者模式没有相应的机制让观察者知道所观察的目标对象是怎么发生变化的，而仅仅只是知道观察目标发生了变化。

> 这些缺点如何完善呢? 编码时注意避免循环, 通过发布订阅模式通知状态, 但是 1 还是不能解决

**使用场景：**

- 一个抽象模型有两个方面，其中一个方面依赖于另一个方面。将这些方面封装在独立的对象中使它们可以各自独立地改变和复用。
- 一个对象的改变将导致其他一个或多个对象也发生改变，而不知道具体有多少对象将发生改变，可以降低对象之间的耦合度。
- 一个对象必须通知其他对象，而并不知道这些对象是谁。
- 需要在系统中创建一个触发链，A 对象的行为将影响 B 对象，B 对象的行为将影响 C 对象……，可以使用观察者模式创建一种链式触发机制。

**注意事项：**

1. JAVA 中已经有了对观察者模式的支持类。
2. 避免循环引用。
3. 如果顺序执行，某一观察者错误会导致系统卡壳，一般采用异步方式。

## 实现

```js
const Subject = (() => {
  const observers = [];
  const addOb = (ob) => {
    observers.push(ob);
  };
  const notify = () => {
    for (let ob of observers) {
      if (typeof ob.update === 'function') {
        ob.update();
      }
    }
  };
  return {addOb, notify};
})();

let subA = {
  update: () => {
    console.log('updateSubA');
  }
},
    subB = {
      update: () => {
        console.log('updateSubB');
      }
    };
Subject.addOb(subA);    //添加观察者subA
Subject.addOb(subB);    //添加观察者subB
Subject.notify();       //通知所有观察者 'updateSubA updateSubB'
```

# 发布订阅模式

## 概念

是一种消息范式，消息的发送者（称为发布者）不会将消息直接发送给特定的接收者（称为订阅者）。而是将发布的消息分为不同的类别，无需了解哪些订阅者（如果有的话）可能存在。同样的，订阅者可以表达对一个或多个类别的兴趣，只接收感兴趣的消息，无需了解哪些发布者（如果有的话）存在。

发布者状态更新时，发布某些类型的通知，只通知订阅了 **相关类型的订阅者**。发布者和订阅者之间是没有直接关联的。

![img](/img/user/programming/basic/cs-basic/design-pattern/event-bus/16aac43ea16815d9.jpg)

如上图所示，发布者与订阅者直接不是互相依赖和关联的，两者之间有一个通信结构（事件通道）。这个事件通道会处理发布者发布的不同类型的通知，并且将这些通知发送给相应的订阅者。

## 实现

```js
const PubSub = (() => {
  const topics = {};  //保存订阅主题
  const subscribe = (type, fn) => {   //订阅某类型主题
    if (!topics[type]) {
      topics[type] = [];
    }
    topics[type].push(fn);
  };
  const publish = (type, ...args) => {    //发布某类型主题
    if (!topics[type]) {
      return;
    }
    for (let fn of topics[type]) {      //通知相关主题订阅者
      fn(...args);
    }
  };
  return {subscribe, publish};
})();

let subA = {type: 'event1'},
    subB = {type: 'event2'},
    subC = {type: 'event1'};

PubSub.subscribe(subA.type, () => console.log(`update eventType: ${subA.type} subA`));   //订阅者A订阅topic1
PubSub.subscribe(subB.type, () => console.log(`update eventType: ${subB.type} subB`));   //订阅者B订阅topic2
PubSub.subscribe(subC.type, () => console.log(`update eventType: ${subC.type} subC`));   //订阅者C订阅topic1
PubSub.publish(subA.type);  //发布topic通知，通知订阅者A、C

```

## 手写一个 EventEmitter

[JavaScript.md](https://github.com/CavsZhouyou/Front-End-Interview-Notebook/blob/master/JavaScript/JavaScript.md#163-eventemitter-%E5%AE%9E%E7%8E%B0)

```js
class EventEmitter {
  constructor() {
    this.events = {};
  }

  on(event, callback) {
    let callbacks = this.events[event] || [];
    callbacks.push(callback);
    this.events[event] = callbacks;

    return this;
  }

  off(event, callback) {
    let callbacks = this.events[event];
    this.events[event] = callbacks && callbacks.filter(fn => fn !== callback);

    return this;
  }

  emit(event, ...args) {
    let callbacks = this.events[event];
    callbacks.forEach(fn => {
      fn(...args);
    });

    return this;
  }

  once(event, callback) {
    let wrapFun = (...args) => {
      callback(...args);

      this.off(event, wrapFun);
    };
    this.on(event, wrapFun);

    return this;
  }
}
```

# 观察者模式与发布订阅模式

## 联系

广义上来说，观察者模式和发布 - 订阅模式，都是一个对象的状态发生变化，通知相关联的对象。所以广义上来说，这两种模式是相似的，正如《Head First 设计模式》所说。

## 区别

![img](/img/user/programming/basic/cs-basic/design-pattern/event-bus/16aac6aa330bdcc4.jpg)

先来看一张图，左边是观察者模型，右边是发布 - 订阅者模型。结合这个图和上文的分析，我们可以总结下这两者的区别。

+ 观察者模式中，被观察者（可理解为发布者）与观察者（可理解为订阅者），这两者之间是 **直接关联**、**互相依赖** 的。而发布 - 订阅模式中，发布者与订阅者是不直接关联的，它们之间多了一个事件通道，通过这个事件通道把发布者和订阅者关联起来。
+ 观察者模式中，被观察者发布通知，**所有观察者** 都会收到通知。发布 - 订阅模式中，发布者发布通知，**只有特定类型的订阅者** 会收到通知。
+ 观察者模式中，被观察者发出状态更新通知后，观察者调用自身内部的更新方法。观察者只知道被观察者状态发生了变化，而不知道发生了什么变化。发布 - 订阅模式中，订阅者的更新是通过事件通道进行细节处理和响应更新的，不同的事件就对应着不同的变化

# PubSub @@@

参考： https://juejin.im/post/5b763528e51d45559e3a5b64

pubsub-js： https://github.com/mroderick/PubSubJS/blob/master/src/pubsub.js

## constructor()

```js
  export default class PubSub {
    constructor() {
      this.eventList = {};
    }
  }
  ```

我们得到了一个全新的类，我们将 `this.eventList` 默认设置为空对象。`this.eventList` 对象将保存我们的具名事件。

## subscribe()

给类的原型添加一个 `subscribe()` 方法

类似：添加事件监听

**参数**

1. eventName：String ，事件名
2. handler：Func ，处理该事件的回调函数

**返回值** : 当前事件绑定的回调函数的个数

**实现**

```js
  /**
   * @param  {String} event 
   * @param  {Function} handler 
   *
   * @api private
   */
  subscribe(event, handler) {
    // 如果该事件不存在，则创建该事件
    if(!this.eventList.hasOwnProperty(event)) {
      this.eventList[event] = [];
    }
    // 将回调函数添加到该事件维护的列表中
    return this.eventList[event].push(handler);
  }
```

**作用** : 将回调函数推入事件队列

## publish()

给类的原型添加一个 `publish()` 方法

类似：触发事件，需要我们 **主动触发**

**参数**

1. eventName：String ，事件名
2. ...rest

**异常**: 如果该事件没有订阅，报错

**实现**

```js
  /**
   * @param  {String} event 
   * @api private
   */
  	publish(event,...rest) {
    if(!this.eventList.hasOwnProperty(event)) {
      // return error
      return new Error('error')
    }
    // 遍历执行该事件的回调函数列表
    this.eventList[event].forEach(callback => callback(...rest));
  }
```

**作用**: 遍历执行该事件的回调函数列表

## unsubscribe()

给类的原型添加一个 `unsubscribe()` 方法

类似：解除事件绑定

**参数**

+ eventName：String ，事件名
+ handler：Func，回调函数的引用

**实现**

```js
  /**
   * @param  {String} event 
   * @param  {Function} handler 
   * @api private
   */
  	unsubscribe(event,handler){
    const handlers = this.eventList[event]
    if(handlers===undefined) return
  
    const handlerIndex = handlers.indexOf(handler)
    handlers.splice(handlerIndex,1)
  }
```

## once()

subscribe()，包装一个函数，unsubscribe()

```js
  once(event,handler){
    const self = this
    function func(...rest){
      handler.apply(,rest)
      self.unsubscribe(event,fuc)
    }
    this.subscribe(event,func)
  }
```

## 完整代码

```js
  export default class PubSub {
    constructor() {
      this.eventList = {};
    }
    /**
     * @param  {String} event 
     * @param  {Function} handler 
     * @api private
     */
    subscribe(event, handler) {
      if(!this.eventList.hasOwnProperty(event)) {
        this.eventList[event] = [];
      }
      return this.eventList[event].push(handler);
    }
    /**
     * @param  {String} event 
     * @api private
     */
    publish(event,...rest) {
      if(!this.eventList.hasOwnProperty(event)){
        console.log('事件未定义');
        return
      }
  
      this.eventList[event].forEach(handler => handler(...rest));
    }
    /**
     * @param  {String} event 
     * @param  {Function} handler 
     * @api private
     */
    unsubscribe(event,handler){
      const handlers = this.eventList[event]
      if(handlers===undefined) return
  
      const handlerIndex = handlers.indexOf(handler)
      handlers.splice(handlerIndex,1)
      console.log('成功解除事件绑定');
    }
  }
```

## 测试

```js
  import PubSub from './lib/pubsub/index.js'
  
  let pubSub = new PubSub()
  
  const testHandler = (...rest) => {
    rest.forEach((val) => {console.log(val)})
  }
  
  pubSub.subscribe('test',testHandler)
  
  pubSub.publish('test','hello','world')
  
  pubSub.unsubscribe('test',testHandler)
  
  pubSub.publish('test2','hello','world')
  
  pubSub.once('once',testHandler)
  pubSub.publish('once','once','once')
  pubSub.publish('once','two','two')
```

