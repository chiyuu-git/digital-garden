---
{"dg-publish":true,"permalink":"/programming/font-end/framework/engineering/unit-test/"}
---


# Unit-test 和 e2e 测试

unit-test 是更细粒度的, 针对单组件和单功能

e2e 站在用户角度的测试, 把程序当作一个黑盒子

[Test Driven Development 一定要用单元测试来实现吗？单元测试是否会影响工作效率？ - 知乎](https://www.zhihu.com/question/27081528/answer/1872155062)

# Jasmine 测试指南

参考：

https://juejin.im/post/5e2c113ae51d4557e86e9b75#heading-10

https://www.ibm.com/developerworks/cn/web/1404_changwz_jasmine/index.html

Jasmine 是一个流行的 JavaScript 测试框架。这篇文章旨在解释测试和测试驱动开发的概念，说明为什么测试如此重要，以及从入门到高阶如何写测试。目标受众是已经了解一些 JavaScript 用法的人群，比如闭包回调原型链。

## 测试驱动开发 （TDD）

一种相对较新的开发方式，过程是: 1 写一部分测试用例，这个时候你还没写代码，测试都是失败状态； 2 然后接着写代码，这些代码保证第一步的测试都通过； 3 所有测试通过后，重新审视代码重构提升代码质量。

## 行为驱动开发（BDD）

关于行为驱动测试有关键两点： 1 测试非常小并且一次只策一件事； 2 测试描述能够组成一个句子，测试框架会替你自动完成这个工作。

## 各阶段单测的价值

![image-20200720095253925](/img/user/programming/font-end/framework/engineering/unit-test/image-20200720095253925.png)

第一阶段：起步

核心方法未确定，框架结构不稳定，不建议写 unit test

第二阶段：增长

核心方法已确定，潜在问题的权重大于写 unit test 的代价，建议全量覆盖

第三阶段：成熟

正确性最为重要，不但要全量覆盖 unit test，而且要对增量代码覆盖 unit test

第四阶段：衰退

用户量逐渐减少，进入维护阶段，unit test 优先级不高

## 基础示例

```js
// src/hello.js
function helloWorld(){
    return "Hello, World";
}
```

```js
// hello.spec.js
describe("Hello World", function() {
    it ("say hello", function() {
        expect(helloWorld()).toEqual("Hello World!");
    });
});
```

专业术语里，把 describe 包含的块叫做 suite，把 it 包含的块叫做 specification，也简称为 spec，在一个 suite 里面可以包含多个数量的 spec，但是也要注意结构化语义化。

在测试文件里，我们的期望 expect 语句使用了 toEqual(),这叫做 matcher，如果我们希望的不是全等而是包含关系，可以使用 toContain()

JavaScript 的作用域的规则适用，所以在 describe 定义的变量对 Suite 中的任何 it 代码块都是可见的。

### TDD 示例

我们以写一个 disemvowel 为例，disemvowel 是去除元音字母的意思，我们要写的测试包括：

```
1 应该去除所有的元音小写字母 "Hello, World!"应该变成 "Hll, Wrld!"
2 应该去除所有的元音大写字母 "Apple juice!"应该变成 "ppl, jc"
3 不应该改变空字符串, ""仍然保持为""
4 也不应该改变没有元音字母的字符串 "Mhmm"仍然是"Mhmm"
```

新建一个测试文件，把我们的测试写上

```js
// spec/Disemvowel.spec.js
describe("Disemvoweler", function() {
    it("should remove all lowercase vowels", function(){
        expect(disemvowel("Hello world")).toEqual("Hll wrld");
    });
    it("should remove all uppercase vowels", function(){
        expect(disemvowel("Apple juice")).toEqual("ppl jc");
    });
    it("should not change empty strings", function() {
        expect(disemvowel("")).toEqual("");
    });
    it("should not change strings with no vowels", function(){
        expect(disemvowel("Mhmm")).toEqual("Mhmm");
    });
});
```

## 编写原则

### 单一情形

每一个 spec 都应该一次只测试一种情形，这样失败的时候就可以快速定位。

### 黑盒测试

当你在专注对行为进行测试的时候，可以把你的项目想象成一个黑盒，只需要关注它的功能，而不需要纠结内部的实现。一个简单的例子是定义一个 person 对象，它分别有一个内部方法和公共方法

```js
var person = {
  // Private method
  _generateHello: function() {
      return "hello";
  },
  // Public method
  helloWorld: function() {
      return this._generateHello() + " world";
  }
};
```

## Matchers

### toEqual()

```js
expect(true).toEqual(true);
expect([1, 2, 3]).toEqual([1, 2, 3]);
expect({}).toEqual({});
```

### toBe()

toBe() 和 toEqual 看起来很相像，但不完全相同。toBe 是检查两个对象是否为同一个，而不仅仅是看它们的值是否相同。

```js
var spot = { species: "Border Collie" };
var cosmo = { species: "Border Collie" };
expect(spot).toEqual(cosmo);  // success; equivalent
expect(spot).toBe(cosmo);     // failure; not the same object
expect(spot).toBe(spot);      // success; the same object
```

### toBeTruthy() toBeFalsy()

```js
expect(true).toBeTruthy();
expect(12).toBeTruthy();
expect({}).toBeTruthy();

expect(false).toBeFalsy();
expect(null).toBeFalsy();
expect("").toBeFalsy();
```

它的语法和 JavaScript 相同，比如下面这些值都为 false

```
false
0
""
undefined
null
NaN
```

加上 not 对匹配方法取反

```js
expect(foo).not.toEqual(bar);
expect("Hello planet").not.toContain("world");
```

### toContain()

检测是否包含使用 toContain

```js
expect("Hello world").toContain("world");
expect(favoriteCandy).not.toContain("Almond");
```

### toBeDefined toBeUndefined

```js
var somethingUndefined;
expect("Hello!").toBeDefined();          // success
expect(null).toBeDefined();             // success
expect(somethingUndefined).toBeDefined();// failure

var somethingElseUndefined;
expect(somethingElseUndefined).toBeUndefined();  // success
expect(12).toBeUndefined();                      // failure
expect(null).toBeUndefined();                    // failure
```

### toBeNull toBeNaN

### 比较方法

toBeGreaterThan toBeLessThan，注意这两个方法也适用于字符串

```js
expect(8).toBeGreaterThan(5);
expect(5).toBeLessThan(12);
expect("a").toBeLessThan("z");
```

### 近似值

toBeCloseTo 第二个参数是保留几位小数的意思

```js
expect(12.34).toBeCloseTo(12.3, 1); // success
expect(12.34).toBeCloseTo(12.3, 2); // failure
expect(12.34).toBeCloseTo(12.3, 3);  // failure
expect(12.34).toBeCloseTo(12.3, 4);  // failure
expect(12.34).toBeCloseTo(12.3, 5);  // failure

expect(12.3456789).toBeCloseTo(12, 0);   // success
expect(500).toBeCloseTo(500.087315, 0);  // success
expect(500.087315).toBeCloseTo(500, 0);  // success
```

### 正则表达式

```js
expect("foo bar").toMatch(/bar/);
expect("horse_ebooks.jpg").toMatch(/\w+.(jpg|gif|png|svg)/i);
expect("jasmine@example.com").toMatch("\w+@\w+\.\w+");
```

### toThrow

https://ajsblackbelt.wordpress.com/2014/05/18/jasmine-tests-expect-tothrow/

抛出错误的方法是不需要执行的，但是问题在于报错的语句不会算被覆盖了

```js
var throwMeAnError = function() {
    throw new Error();
};
expect(throwMeAnError).toThrow();

intersectionObserver._observerId = 1
expect(intersectionObserver.relativeTo).toThrow()
intersectionObserver._observerId = null
```

自定义抛错的参数，同函数做一层包装，同时可以保证报错的行被覆盖

```js
it('Relative nodes cannot be added after "observe" call in IntersectionObserver', () => {
    expect(() => {
        intersectionObserver._observerId = 1
        intersectionObserver.relativeTo()
    }).toThrow()
})
```

### 自定义匹配方法

```js
beforeEach(function() {
  this.addMatchers({
    toBeLarge: function() {
      this.message = function() {
        return "Expected " + this.actual + " to be large";
      };
        return this.actual > 100;
    }
  }); 
});
```

这个匹配方法接收两个参数\

```js
beforeEach(function() {
  this.addMatchers({
    toBeWithinOf: function(distance, base) {
      this.message = function() {
        var lower = base - distance;
          var upper = base + distance;
            return "Expected " + this.actual + " to be between " +
              lower + " and " + upper + " (inclusive)";
    };
          return Math.abs(this.actual - base) <= distance;
      }
  }); 
    
});
```

## Setup and Teardown

为了使某个测试用例干净的重复 setup 和 teardown 代码， Jasmine 提供了全局的 beforeEach 和 afterEach 方法。正像其名字一样，beforeEach 方法在 describe 中的

每个 Spec 执行之前运行，afterEach 在每个 Spec 调用后运行。

这里的在同一 Spec 集合中的例子有些不同。测试中的变量被定义为全局的 describe 代码块中，用来初始化的代码被挪到 beforeEach 方法中。afterEach 方法在继续前重置这些变量。

```JS
describe("An example of setup and teardown)", function() {
  var gVar;
 
  beforeEach( function() {
    gVar = 3.6;
    gVar += 1;
  });
 
  afterEach( function() {
    gVar = 0;
  });
 
  it("after setup, gVar has new value.", function() {
    expect(gVar).toEqual(4.6);
  });
 
  it("A spec contains 2 expectations.", function() {
    gVar = 0;
    expect(gVar).toEqual(0);
    expect(true).toEqual(true);
  });
});
```

### 嵌套代码块

describe 可以嵌套， Specs 可以定义在任何一层。这样就可以让一个 suite 由一组树状的方法组成。在每个 spec 执行前，Jasmine 遍历树结构，按顺序执行每个 beforeEach 方法。Spec 执行后，Jasmine 同样执行相应的 afterEach。

```JS
describe("A spec", function() {
  var gVar;
 
  beforeEach(function() {
    gVar = 3.6;
    gVar += 1;
  });
 
  afterEach(function() {
    gVar = 0;
  });
 
  it("after setup, gVar has new value.", function() {
    expect(gVar).toEqual(4.6);
  });
 
  it("A spec contains 2 expectations.", function() {
    gVar = 0;
    expect(gVar).toEqual(0);
    expect(true).toEqual(true);
  });
 
  describe("nested describe", function() {
    var tempVar;
 
    beforeEach(function() {
      tempVar = 4.6;
    });
 
    it("gVar is global scope, tempVar is this describe scope.", function() {
      expect(gVar).toEqual(tempVar);
    });
  });
});
```

## 更多 Jasmine 特性

- Before and After https://www.ibm.com/developerworks/cn/web/1404_changwz_jasmine/index.html
- 嵌套的 suite https://www.ibm.com/developerworks/cn/web/1404_changwz_jasmine/index.html
- 跳过某些测试 xit xdescribe https://www.ibm.com/developerworks/cn/web/1404_changwz_jasmine/index.html
- 匹配类名 any

```js
expect(rand()).toEqual(jasmine.any(Number));
expect("Hello world").toEqual(jasmine.any(String));
expect({}).toEqual(jasmine.any(Object));
expect(new MyObject).toEqual(jasmine.any(MyObject));
```

## Spies

https://juejin.im/post/5e2c113ae51d4557e86e9b75#heading-10

Spy 用于模拟函数的调用，并且记录被调用的次数以及传递的参数，我们将这样的测试称为函数的元数据测试。例如：

```js
            spyOn(selectQuery, '_querySlaveSelector')
            selectQuery.exec(noop)
            expect(selectQuery._querySlaveSelector).toHaveBeenCalled();
```

### 替换

https://jasmine.github.io/api/edge/Spy.html

可以简单粗暴的定义入参和返回值

```js
spyOn(someObj, 'func').withArgs(1, 2, 3).and.returnValue(42);
someObj.func(1, 2, 3); // returns 42
```

## 异步测试

Jasmine 支持测试需要执行异步操作的 specs，调用 beforeEach, it, 和 afterEach 的时候，可以带一个可选的参数 `done`，当 spec 执行完成之后需要调用 done 来告诉 Jasmine 异步操作已经完成。默认 Jasmine 的超时时间是 5s，可以通过全局的 `jasmine.DEFAULT_TIMEOUT_INTERVAL` 设置。

```js
describe("Jasmine 异步测试演示", function() {
    let value;

    beforeEach(function(done) {
        setTimeout(function() {
            value = 0;
            // 只有执行done函数,后面的it才会执行
            done();
        }, 1);
    });

    it("should support async execution of test preparation and expectations", function(/*done*/) {
        value++;
        expect(value).toBeGreaterThan(0);
        // done(); 只要参数没有done，这个done是可以省略的，这是未来保证后面的it不会提前执行
    });
});
```

# sinon.js

参考：https://juejin.im/post/5b32f45851882574e10e02eb

## Sinon 是什么

简单的说，[Sinon](http://sinonjs.org/) 允许你去替换代码中复杂的部分，以此来简化你的测试代码。

当我们测试某部分代码时，你不希望受到其它部分的影响。如果有外部因素影响测试，那么测试项将变得非常复杂且不稳定。

如果你想测试一个使用了 ajax 的代码，你该怎么做？你需要跑一个服务端，并保证该服务端返回指定的响应数据来支撑你的测试项。这很难完成也让运行测试很麻烦。

那如果你的代码依赖时间呢？假如它需要等待一秒钟才执行。怎么办？你需要在你的测试项中使用 `setTimeout`，但这会让测试变得缓慢。想像一下，如果间隔时间很久，例如五分钟。我想你不会希望每次跑测试项都等待五分钟吧。

如果使用 Sinon，我们可以搞定这些问题（甚至更多），并减少复杂度。

## Sinon 是怎么工作的？

Sinon 通过允许我们简单的创建 `test-doubles` 从而帮助我们减少测试项编写的复杂度。

正如它名字一样，Test-doubles 作用是在测试中替换某部分代码。上面提到的 ajax 的例子中，不需要创建服务端，我们可以使用 `test-doubles` 替换掉 Ajax 调用。在 timer 例子中，我们可以使用 `test-doubles` 来控制时间。

听起来可能很复杂，但基本思想很简单。基于 javascript 的动态性，我们可以替换任何函数。Test-doubles 只是在这个思想的基础上走的更远了一些。使用 Sinon，我们可以使用 test-doubles 替换任何 javascript 函数，并提供很多方便测试的配置。

Sinon 中 `test-doubles` 分三类：

- [Spies](http://sinonjs.org/docs/#spies)，提供了函数调用的信息，但不会改变其行为（译者注：类似动态代理）
- [Stubs](http://sinonjs.org/docs/#stubs)，类似 Spies，但是是完全替换目标函数。这可以让你随心所欲的控制函数–抛异常，返回指定结果等
- [Mocks](http://sinonjs.org/docs/#mocks)，提供了替换整个对象的能力

此外，Sinon 还提供了其他的辅助功能，本文不包含下面的范围：

- [Fake timers](http://sinonjs.org/docs/#clock)，用来控制时间，例如触发一个 `setTimeout`
- [Fake XMLHttpResquest and server](http://sinonjs.org/docs/#server)，可以用来伪造 Ajax 请求和响应

基于这些功能，Sinon 可以让你解决测试中遇到的由外部依赖带来的所有复杂问题。如果你学会了 Sinon 提供的这些技巧，你几乎不需要其它别的工具了。

## Spies

Spy：间谍，从事间谍活动，搜集情报

Spies 很简单，但其它很多功能依赖它。

spies 的主要用法是收集函数的调用信息。你可以用来验证一些事儿，例如函数是否被调用。

```js
var spy = sinon.spy();

//我们可以像调用函数一样调用spy
spy('Hello', 'World');

//我们可以得到调用信息
console.log(spy.firstCall.args); //output: ['Hello', 'World']
```

`sinon.spy` 函数返回一个 `Spy` 对象，该对象可以像函数一样被调用，它记录每次被调用信息。在上面的例子里，`firstCall` 属性包含了第一次调用的信息，例如 `firstCall.args` 表示调用时的参数列表。

虽然你可以像上面例子那样创建一个匿名 spies，但通常情况下你需要使用 `spy` 替换一个其它函数。

```js
var user = {
  ...
  setName: function(name){
    this.name = name;
  }
}

//为user.setName创建一个spy
var setNameSpy = sinon.spy(user, 'setName');

//现在，每次调用目标函数，spy都会记录相关信息
user.setName('Darth Vader');

//我们可以使用spy对象查看相关信息
console.log(setNameSpy.callCount); //output: 1

//非常重要的步骤--拆除spy
setNameSpy.restore();
```

上面例子展示了使用 spy 替换其它函数的写法，最重要的一点是：当你确定不再需要 spy 后，你记得恢复原始函数，参考例子中的最后一行。不然测试可能出现非预期行为。

Spies 包含许多不同的属性，用来提供不同的信息。[spy文档](http://sinonjs.org/docs/#spies-api) 列出了完整的属性列表。

在实际场景中，你可能不会经常使用 spies。你更多时候使用的是 stub，但是 spies 用来检测函数是否被调用非常方便：

```js
function myFunction(condition, callback){
  if(condition){
    callback();
  }
}

describe('myFunction', function() {
  it('should call the callback function', function() {
    var callback = sinon.spy();

    myFunction(true, callback);

    assert(callback.calledOnce);
  });
});
```

## Sinons 断言

在我们介绍 stubs 之前，我们快速看一下 [sinon断言](http://sinonjs.org/docs/#assertions)。

使用 sinon 断言我们可以得到更多有价值的错误信息。这在当你验证比较复杂的条件时非常有用，例如函数的参数。

下面列出一些 sinon 提供的其它强大断言的一些例子：

- `sinon.assert.calledWith` 可以用来验证函数是否使用指定的参数（这可能是我用的最多的一个）
- `sinon.assert.callOrder` 用来验证函数的调用顺序

[sinon断言文档](http://sinonjs.org/docs/#assertions) 介绍了所有的内容。如果你喜欢使用 Chai，有一个 [sinon-chai-plugin](https://github.com/domenic/sinon-chai) 可以让你通过 chai 的 `expect` 和 `should` 接口来使用 sinon 断言。

## Stubs

stubs 归类于 test-doubles 是因为它的灵活和方便性。它拥有 spies 的全部功能，此外它还彻底的替换掉了目标函数。换句话说，当你使用 spy，原始的函数依然会被调用，但如果使用 stub，原始函数就不会被执行了。

这个特性让 stub 可以胜任许多任务，例如：

- 替换像 ajax 或其它外部函数等让测试变复杂或慢的调用
- 根据函数的响应来触发不同的代码流程
- 测试不寻常的条件，如抛出异常

我们可以像创建 spies 一样创建 stubs：

```js
var stub = sinon.stub();

stub('hello');

console.log(stub.firstCall.args); //output: ['hello']
```

我们创建了一个匿名的 stubs，但用 stubs 来替换存在的函数更有意义。

举个例子，如果你有一段代码调用了 jquery 的 Ajax，测试它将变得麻烦。代码会发送请求到我们配置的服务端，所以我们需要保证服务端的有效性，或者给代码添加特定的分支来适配测试环境 – 这么做真的大错特错。你不应该在代码中编写任何测试特定逻辑。

我们可以使用 sinon 的 stub 来替换 ajax 调用。这会让测试变得简单。

下面的例子中，我们使用 ajax 向预定 url 发送一个携带参数的请求。

```js
function saveUser(user, callback) {
  $.post('/users', {
    first: user.firstname,
    last: user.lastname
  }, callback);
}
```

通常，测试这个函数将变的很麻烦，但我们有了 stub，一切变得美好。

假如我们想要确保传递给 `saveUser` 函数的回调方法在请求结束后正确的被执行了一次。

```js
describe('saveUser', function() {
  it('should call callback after saving', function() {

    //We'll stub $.post so a request is not sent
    var post = sinon.stub($, 'post');
    post.yields();

    //We can use a spy as the callback so it's easy to verify
    var callback = sinon.spy();

    saveUser({ firstname: 'Han', lastname: 'Solo' }, callback);

    post.restore();
    sinon.assert.calledOnce(callback);
  });
});
```

这里，我们将 ajax 函数替换成了 stub。这意味着请求不会被发送，我们不需要一个服务端 – 我们全权控制了我们的测试代码！

介于我们想确认我们传给 `saveUser` 的回调会被执行，我们让 stub 立刻返回。这意味着 stub 将自动调用 callback 参数。这模仿了 `$.post` 在请求完成后的行为。

除了 stub，我们还创建了一个 spy。我们可以使用一个普通的函数作为回调，但使用 spy 会让 `sinon.assert.calledOnce` 更方便验证测试结论。

大多数需要 stub 的场景，都类似下面步骤：

- 确认是否包含问题函数，例如 `$.post`
- 观察并掌握其行为
- 创建一个 stub
- 让 stub 来模拟目标行为

stub 不需要模拟所有的行为，只需要足够你的测试项使用即可，其它细节可以忽略。

另外一些 stub 的常用场景是验证一个函数是否使用特定的参数。

举个例子，在我们的 ajax 函数中，我们希望确定正确的数据被提交。因此，我们可能会这么做：

```js
describe('saveUser', function() {
  it('should send correct parameters to the expected URL', function() {

    //We'll stub $.post same as before
    var post = sinon.stub($, 'post');

    //We'll set up some variables to contain the expected results
    var expectedUrl = '/users';
    var expectedParams = {
      first: 'Expected first name',
      last: 'Expected last name'
    };

    //We can also set up the user we'll save based on the expected data
    var user = {
      firstname: expectedParams.first,
      lastname: expectedParams.last
    }

    saveUser(user, function(){} );
    post.restore();

    sinon.assert.calledWith(post, expectedUrl, expectedParams);
  });
});

```

see the pen [Sinon Tutorial: JavaScript Testing with Mocks, Spies & Stubs](http://codepen.io/SitePoint/pen/eZNKqZ/)

这次，我们有创建了一个 `$.post()` 的 stub，但这回我们并没有让它直接返回。这次我们的测试目标不是回调，因此让它返回并不是必须的。

我们设置了一些变量来存期望的数据 - url 和参数。这是一个好的实践，让我们很容易知道什么是测试必须的。也可以帮助我们减少重复代码。

这次我们使用 `sinon.assert.calledWith()` 断言。我们将 stub 传递进去，因为我们想确定 stub 包含了正确的参数。

使用 sinon，还有其它的方法来测试 ajax 请求。例如使用 sinon 的伪造 XMLHttpResquest 功能。我们不会在这里去介绍细节，如果你想了解更多可以参考 [my article on Ajax testing with Sinon’s fake XMLHttpRequest](http://codeutopia.net/blog/2015/03/21/unit-testing-ajax-requests-with-mocha/)。

置换指定函数

```js
var testObj = {
	'fun' : function (arg) {}	
}
 
// // 将 testObj.fun 替换成一个stub，使用完毕后需要调用stub.restore() 或 testObj.fun.restore 复原。
var stub = sinon.stub(testObj, 'fun');
// 将testObj.fun 替换成指定的函数
// var stub = sinon.stub(testObj, "fun").callsFake(
//	function (argument) {}
//)
stub();

expect(stub.callCount).to.be.equal(1);
```

## Mocks

Mocks 不同于 stubs。如果你之前听过 `mock object` 这个术语，那没错了 - sinon 的 mocks 用来替换整个对象，并改变其行为。

如果你需要替换某个对象的多个方法，你就应该使用 mocks。如果你只是希望替换某个单独的方法，stub 更方便。

使用 mocks 时你需要小心！因为它太 TM 强大了，很容易让你的测试过于特定 - 测试的太细或太刻意 - 从而让你的测试太容易过期。

与 spies 和 stubs 不同，mocks 包含内建的断言。当使用 mock 对象时，你可以定义你期望的结果，你期望的行为。

假设我们使用 [store.js](https://github.com/marcuswestin/store.js) 来保存一些数据到 localstorage，我们打算测试这个特性。我们可以使用 mock 来写测试：

```js
describe('incrementStoredData', function() {
  it('should increment stored value by one', function() {
    var storeMock = sinon.mock(store);
    storeMock.expects('get').withArgs('data').returns(0);
    storeMock.expects('set').once().withArgs('data', 1);

    incrementStoredData();

    storeMock.restore();
    storeMock.verify();
  });
});
```

调用 mock 对象的 `mock.expects(something)` 会创建一个期望值。意味着 `mock.something()` 方法期望被调用。Each expectation, in addition to mock-specific functionality, supports the same functions as spies and stubs.（译者注：只能意会无法言表啊）

你可能会觉得通常 stub 都比 mock 更简单 - 没错。Mocks 要小心使用。

mock 特定的特性，可以查看 [sinon的mock文档](http://sinonjs.org/docs/#mocks)。

## Stub Module Function

esmodule 暴露的模块都是只读的，你无法改变他们，而且这些也会受到 babel 和 webpack 的影响，打包过后的函数，需要准确的 mock 打包后的那个才行

也就是说我的写法是单测不友好的，无法测试

commonjs 暴露的模块是可读写的，因此可以直接 mock？

## 重要的最佳实践：使用 sinon.test()

这里有个使用 sion 的很重要的最佳实践，不管是使用 spies，stubs 还是 mocks 都应该牢记。

如果你用 test-doubles 替换了一个存在的函数，则使用 `sinon.test()`。

前面的例子中，我们使用 `stub.restore()` 或 `mock.restore()` 来在我们使用完后清理它们。这很有必要，否则 test-doubles 将持续有效，这将可能影响其他的测试项并导致错误。

但是，直接使用 `restore()` 可能很难，有可能因为某个异常导致 `restore()` 没有被调用！

我们有两种方法来解决这个问题：我们可以自己包装完整的 `try catch` 块。这允许我们将 `restore()` 放在 `finally` 块中调用来确保一切正常。

或者，一个更好的做法是我们可以将测试体写在 `sinon.test()` 中：

```js
it('should do something with stubs', sinon.test(function() {
  var stub = this.stub($, 'post');

  doSomething();

  sinon.assert.calledOnce(stub);
});

```

上面的代码中，注意 `it()` 的第二个参数，它被 `sinon.test()` 包裹。此外注意我们使用 `this.stub()` 代替了 `sinon.stub()`。

使用 `sinon.test()` 包裹测试体可以让我们使用 sinon 沙盒特性，其允许我们使用 `this.spy()`，`this.stub()` 和 `this.mock()` 来创建 spies， stubs 和 mocks。任何你在沙盒中创建的 test-doubles 都会自动被清理。

我们上面的代码中并没有 `stub.restore()` – 托沙盒的福它已经不再需要了。

请尽可能使用 `sinon.test()`，你会避免由于前面的测试项没有清理 test-doubles 而导致的灵异问题。

## Sinon 并不是黑魔法

Sinon 很强大，而且某些时候很难理解它是如何工作的。让我们看一下 Sion 工作原理的原生 javascript 的例子，这样我们可以更好的理解其思想。

我们可以自己实现 spies， stubs 和 mocks。使用 Sinon 只是因为它更方便 – 自己实现会非常复杂。

首先，spy 本质上是一个函数 wrapper：

```js
//A simple spy helper
function createSpy(targetFunc) {
  var spy = function() {
    spy.args = arguments;
    spy.returnValue = targetFunc.apply(this, arguments);
    return spy.returnValue;
  };

  return spy;
}

//Let's spy on a simple function:
function sum(a, b) { return a + b; }

var spiedSum = createSpy(sum);

spiedSum(10, 5);

console.log(spiedSum.args); //Output: [10, 5]
console.log(spiedSum.returnValue); //Output: 15
```

我们可以很容易的使用自定义函数来实现 spy 的功能。但注意 sinon 的 spies 提供了非常多的特性 – 包括断言的支持。这让 sinon 更方便使用。

真实项目的测试有时非常的复杂，导致你可能彻底放弃。但是使用 sinon，测试变得非常简单。

记住一个重要的准则：如果一个函数很难被测试，尝试使用 test-doubles 替换它。

想知道更多关于如何让你的代码使用 sinon？当我的网站来，我会提供 [Sinon in the real-world guide](https://codeutopia.net/go/sinon-pdf-download-page/) 给你，包含了 sinon 的最佳实践，和三个真实的例子来讲解如何在不同的测试方案中使用它。

# 与 Karma 集成

在 Java 中，用 JUnit 做单元测试, 用 Maven 进行自动化单元测试；

同样相对应的 JS 中，则可以用 Jasmine 做单元测试，用 Karma 自动化完成单元测试。

Karma 作为 JavaScript 测试执行过程管理工具，可用于测试所有主流 Web 浏览器。下面简单介绍一下 Karma 与 Jasmine 的集成。

首先，下载安装 Karma。

初始化 karma 配置文件 karma.conf.js。

安装集成包 karma-jasmine。

## Karma Init

```js
karma init karma.conf.js
```

执行命令后，会被问到一系列的问题：

- What testing framework do you want to use? 默认情况下 Jasmine, Mocha, QUnit 都已经被安装了，这里我们可以直接使用它们的名称。如果在应用中用到了其它的测试框架，那就需要我们安装它们所对应的插件，并在配置文件中标注它们（详见 karma.conf.js 中的 plugins 项）
- Do you want to use Require.js? [Require.js](http://www.requirejs.org/) 是异步加载规范（AMD）的实现。常被作为基础代码库，应用在了很多的项目与框架之中，例如 Dojo, AngularJs 等、
- Do you want to capture a browser automatically? 选择需要运行测试用例的浏览器。需要注意的就是，必须保证所对应的浏览器插件已经安装成功。
- What is the location of your source and test files? 选择测试用例所在的目录位置。Karma 支持通配符的方式配置文件或目录，例如 `*.js, test/**/*.js` 等。如果目录或文件使用相对位置，要清楚地是，此时的路径是相对于当前运行 karma 命令时所在的目录。
- Should any of the files included by the previous patterns be excluded? 目录中不包括的那些文件。
- Do you want Karma to watch all the files and run tests on change? 是否需要 Karma 自动监听文件？并且文件一旦被修改，就重新运行测试用例？

最终 Karma 生成如下的配置文件（karma.conf.js），如下

## 配置项详解

Karma 的配置文件支持使用 JavaScript, CoffeeScript 或者 TypeScript 语言来编写。如果没有在执行 karma 命令时指定对应的配置文件，那么它会按照下面的顺序依次去寻找并加载配置文件

- `./karma.conf.js`
- `./karma.conf.coffee`
- `./karma.conf.ts`
- `./.config/karma.conf.js`
- `./.config/karma.conf.coffee`
- `./.config/karma.conf.ts`

### Files – 文件项

使用 files 选项，用于告诉 Karma 哪些文件会被项目使用，而哪些文件含有测试用例，以及需要测试。值得注意的是，文件配置的顺序就是浏览器引用它们时的顺序。

> 可以通过该配置项实现单个文件的测试

Karma 使用 minimatch 库来匹配文件。由于 minimatch 本身以方便灵活而著称，同时它的文件表达式又非常的简洁。在配置文件中，下面的这几个部分都会用到这种表达式

- exclude
- files
- preprocessors

例如

- `**/*.js`: 表示在所有的子目录内，以”js”为后缀的那些文件
- `**/!(jquery).js`: 和上面的含义相同，但不包括”jquery.js”文件
- `**/(foo|bar).js`: 表示在所有子目录内，以”foo.js”或者”bar.js”为名称的那些文件

详细的配置项可以参看 [Karma 官网关于 file 部分](http://karma-runner.github.io/1.0/config/configuration-file.html)

我们以 files 配置项为例，来详细的了解文件匹配的具体用法

**匹配模式**

- 所有的相对位置，都是针对 basePath 而言的.
- basePath 也可以使用相对位置定义，此时它是相对于配置文件所在的位置
- Karma 使用 globa 库来解决文件的位置问题。它支持使用 minimatch 表达式，例如 test/unit/**/*.spec.js.

**匹配顺序**

- 模式的顺序决定了文件包含在浏览器中的顺序
- 如果多个文件被匹配到，文件按照按字母顺序排序
- 每个文件都包含一次。如果多个模式匹配同一个文件，则文件包含在第一个匹配到的模式中

```js
files: [
    // Detailed pattern to include a file. Similarly other options can be used
    {
        pattern: 'lib/angular.js',
        watched: false
    },
    // Prefer to have watched false for library files. No need to watch them for changes
    // simple pattern to load the needed testfiles
    // equal to {pattern: 'test/unit/*.spec.js',
    //watched: true, served: true, included: true}
    'test/unit/*.spec.js',
    // this file gets served but will be ignored by the watcher
    // note if html2js preprocessor is active, reference as
    //`window.__html__['compiled/index.html']`
    {
        pattern: 'compiled/index.html',
        watched: false
    },
    // this file only gets watched and is otherwise ignored
    {
        pattern: 'app/index.html',
        included: false,
        served: false
    },
    // this file will be served on demand from disk and will be ignored by the watcher
    {
        pattern: 'compiled/app.js.map',
        included: false,
        served: true,
        watched: false,
        nocache:true
    }
],
```

### **Browsers – 浏览器配置**

捕获浏览器的行为总是令人感到很沮丧的，无疑这样的工作会耗费大量的开发时间。然而如果使用 Karma，这一切都会变得异常简单。原因是，Karma 都帮你自动完成了！

配置浏览器，我们只需要在配置文件中正确的设置 browsers 项（例如 browsers: [‘Chrome’]），Karma 会管理这些浏览器，包括启动与关闭它们。

Karma 支持的浏览器

- Chrome and Chrome Canary
- Firefox
- Safari
- PhantomJS
- JSDOM
- Opera
- Internet Explorer
- SauceLabs
- BrowserStack
- many more

我们以 Firefox 浏览器为例，首先你需要安装相应的插件

```js
# Install the launcher first with NPM:
$ npm install karma-firefox-launcher --save-dev
```

接下在，在配置文件中加入新支持的浏览器名称

```js
module.exports = function(config) {
    config.set({
        browsers : ['Chrome', 'Firefox']
    });
};
```

默认情况下，在配置文件中 browsers 项是没有被配置的（也就是说它的值是空）

当然，如果你希望使用其他的设备（如 tablet，手机等）来测试的话，只需在设备中打开对应的的浏览器，并访问 `http://<hostname>:<port>` (默认端口 9876)。

另外，可以通过设置 _BIN 环境变量来替换浏览器路径。例如，在 Linux 下修改 Firefox 浏览器路径

```js
# Changing the path to the Firefox binary
$ export FIREFOX_BIN=/usr/local/bin/my-firefox-build
```

详细解释，请参看 [Karma 官网关于浏览器选项的详细配置](http://karma-runner.github.io/1.0/config/browsers.html)

### Preprocessors 配置

Preprocessors 定义的方法，会在文件被浏览器运行前执行（有点类似 AOP（Aspect-Oriented Programming）的概念）。

Preprocessors 的配置方法如下：

https://developer.ibm.com/zh/articles/wa-lo-use-karma-jasmine-build-test-environment/

需要修改：files 和 exclude 变量。其中 autoWatch 设置为 true，这样如果修改测试文件并保存后，Karma 会检测到然后自动执行。

```JS
module.exports = function (config) {
    config.set({
        basePath: '',
        frameworks: ['jasmine'],
        files: ['*.js'],
        exclude: ['karma.conf.js'],
        reporters: ['progress'],
        port: 9876,
        colors: true,
        logLevel: config.LOG_INFO,
        autoWatch: true,
        browsers: ['Chrome'],
        captureTimeout: 60000,
        singleRun: false
    });
};
```

启动 karma，自动执行单元测试。

```
F:\Projects\karma>karma start karma.conf.js
```

另外，Jasmine 也可以与持续集成工具 Jenkins 进行集成

## 完整例子

https://www.ibm.com/developerworks/cn/web/1404_changwz_jasmine/index.html

```js
/**
 * @file convert-to-custom-component-selector.js单测
 * @author pangjing02@baidu.com
 */

import {convertToCustomComponentSelector} from '../../../../src/common/swan-xml/convert-to-custom-component-selector';

describe('should add custom-component prefix', () => {
    const selector = '.active';
    const componentName = 'live-player';
    const uniquePrefix = 'live-player';

    it('convert prefix', () => {
        expect(convertToCustomComponentSelector(
            selector,
            componentName,
            uniquePrefix)
            )
        .toEqual('.live-player__active');
    });
});
```

# Istanbul

https://juejin.im/entry/5a139057f265da430c116f8f

## 忽略

对于 if 条件，可以使用 `/*istanbul ignore if*/` 或 `/* istanbul ignore else*/`，去忽略对应的分支

For all other cases, the Swiss army knife may be used which skips the "next thing" in the source code

The "next" thing may be, among other things:

对于其他情况，可以使用 `/* istanbul ignore next */` 去忽略“下一件事”，这个下一件事可能是：

1. 一个 JS 语句（包括 assignments, ifs, loops, switches, functions)），在这种情况下，将会忽略所有形式的覆盖（行，分支，函数）
2. 一种 switch case 语句，在这种情况下，case 分支的覆盖将忽略，对于 case 分支的内容，将会忽略所有形式的覆盖
3. 三元表达式中的一种条件，在这种情况下，分支被忽略
4. 逻辑表达式的一部分，在这种情况下，分支被忽略

# Jest
