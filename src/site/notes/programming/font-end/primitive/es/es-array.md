---
{"dg-publish":true,"permalink":"/programming/font-end/primitive/es/es-array/"}
---


# Experience

#programming-experience

+ 要特别注意方法是否会影响到原来的数组
+ `nums.length` 是动态变化的，数组中间的删除操作之后要 `i--`，否则可能会跳过元素
+ ![](es-array.md#性能)

# 基本语法

数组也是一个对象，它和我们普通对象类似，不同的是：

- 普通对象用名值对来储存数据，而数组 **还可以** 通过数字来索引 (index)、操作元素。
- 属性和元素只是名字不同, 本质是一样的
- 索引：从 0 开始的整数

为什么数组 index 要从 0 开始, 将下标理解为偏移量, 即将数组的访问理解为: 基址（首元素的地址）+ 偏移量；当偏移量为 0 时，就表示访问首元素。

数组的存储性能比对象要好, 所以在开发中经常用数组来存储一些数据

## 创建数组对象

```js
var arr = new Array(); //也可以将要添加的元素直接传递，传递2个以上
var arr = []; 
typeof arr;//object
arr.name=array1; //也是可以的，但是不会影响length的值
```

**推荐使用：**`Arrary.of()`

![es-array](programming/font-end/primitive/es/es-array.md#Array%20of)

## 向数组中添加元素

```js
array[index] = value；//值可以是任何数据类型，当然也可是是对象
arr[0] = 10;
arr = [{name:””},{ name:””},{ name:””}]
arr = [function(){},function(){}]
//值为数组额，则二维数组，多维数组
```

## 读取数组中的元素

```js
console.log(arr[0]); //10
```

如果读取不存在的索引，不会报错，而是返回 undefined. 负数索引也是合法的

索引和属性很类似

### 获取数组的最后一个元素

#### Pop & Push

先 pop 从返回值中获取, 再 push 回去

#### Length - 1 @recommand

如果数组 length 为 0, 应该拿到 0, 这样拿到 -1, 负数索引? 负数索引是合法的, 所以这种方法最好

如果数组 length 为 1, 也应该拿到 0

如果数组 length 为 n, 应该拿到 n - 1 (n > 0)

#### slice(-1, -2)

返回一个新的数组, 浅引用, 如果是 undefined, 需要重新 push 进去才行

## 定义数组长度

```js
var arr1 = [10]; // 只有一个元素10
var arr2 = new Array(10); //数组长度为10
```

区别在于，**当向数组只传递一个数字的时候**，字面量形式是单个元素，构造函数形式是数组长度

定义数组对的长度意义不大，因为限制数组长度在 JS 里没有多大意义

## Length 属性

设置或返回连续的数组中的长度

- 对于 **非连续** 的数组，会获取到最大的索引 +1

```js
arr[10]=11; //arr.length = 11;
console.log(arr);//非连续数组，中间的内容会为空，以逗号连接
```

修改数组长度：``arr.length = newLength``

- 如果修改后的 length 大于原长度，则多出的部分会空出来
- 如果修改后的 length 小于原长度，则原数组中多出的元素会被删除 @@@

向数组的最后添加元素

- 数组 [数组.length] = 值，length 的值刚好是最大索引 +1，可以做到给最后一位添加元素
- 只能添加一个元素，不如 push()

## 数组的空位

数组的空位指，数组的某一个位置没有任何值。比如，`Array` 构造函数返回的数组都是空位。

```javascript
Array(3) // [, , ,]
```

注意，空位不是 `undefined`，一个位置的值等于 `undefined`，依然是有值的。空位是没有任何值，`in` 运算符可以说明这一点。

```js
0 in [undefined, undefined, undefined] // true
0 in [, , ,] // false
```

上面代码说明，第一个数组的 0 号位置是有值的，第二个数组的 0 号位置没有值。

**由于空位的处理规则非常不统一，所以建议避免出现空位**

## toString()

重写了 `Object.prototype.toString()`

数组的 `toString` 方法返回一个字符串，该字符串由数组中的每个元素的 `toString()` 返回值经调用 join() 方法连接（由逗号隔开）组成。

```js
console.log([null,1,undefined].toString()) // ',1,'
console.log([null,1,undefined].toString()[0]===',') // true
console.log([null,1,undefined].toString()[1]) // '1'
console.log([null,1,undefined].toString()[2]===',') // true
```

# 影响原数组的方法

即是有副作用的方法

## push()

用来向数组的末尾添加 **一个或多个元素**，**并返回数组新的长度** @@@

```js
arr.push(arg1,arg2,arg3...)
```

可以添加多个，比 length 属性方便

只能接受参数列表，ES6 配合扩展运算符可以传递数组

## pop()

用来删除数组的最后 **一个** 元素，并 **返回被删除的元素**

## unshift()

向数组的 **前边** 添加一个或多个元素，并 **返回数组的新的长度**

## shift()

删除数组的 **前边** 的一个元素，并 **返回被删除的元素**

## splice()

可以用来删除数组中指定元素，**并使用新的元素替换**

**参数：**

1. `start` ：删除开始位置的索引，默认值为 undefined，没有传入则不会有任何影响
2. `deleteCount` ：删除的个数

> 如果 `deleteCount` 被省略了，或者它的值大于等于 `array.length - start`(也就是说，如果它大于或者等于 `start` 之后的所有元素的数量)，那么 `start` 之后数组的所有元素都会被删除
>
> 如果 start 和 deleteCount 都被省略，则会返回一个空数组

3. 第三个参数以后，都是替换的元素，这些元素将会插入到 **开始位置索引**
   - 删除 0 个，增加一些，实现增（因为是插入到 **开始位置索引**，可以理解为在前面插入）
   - 删除多个，不增加，实现删
   - 删除 N 个，替换 N 个，实现改

**返回值：** 该方法会将删除的元素封装到 新数组 中返回

## copyWithin()

ES6 新增

数组实例的 `copyWithin` 方法，在当前数组内部，将指定位置的成员复制到其他位置（会覆盖原有成员），然后返回当前数组。也就是说，使用这个方法，会修改当前数组。

```js
Array.prototype.copyWithin(target, start = 0, end = this.length)
```

**接受三个参数**

- target（必需）：从该位置开始替换数据。如果为负值，表示倒数。
- start（可选）：从该位置开始读取数据，默认为 0。如果为负值，表示倒数。
- end（可选）：到该位置前停止读取数据，默认等于数组长度。如果为负值，表示倒数。

## reverse()

可以用来反转一个数组，它会 **对原数组产生影响**

还可以使用 sort 方法模拟，传入的 `compareFunction` 总是返回一个正数即可

## sort()

可以对一个数组中的内容进行排序，默认是按照 Unicode 编码进行排序，调用以后，会 **直接修改原数组**。

可以自己指定排序的规则，需要一个回调函数作为参数，浏览器会传入数组的元素作为实参调用回调函数

> ECMAScript 没有定义用哪个排序算法，所以浏览器厂商可以自行去实现算法。例如，Mozilla Firefox 使用归并排序作为 Array.prototype.sort 的实现，而 Chrome 使用了一个快速排序（下面我们会学习的）的变体。

回调函数的返回值：

- 大于 0，交换位置
- 小于等于 0，位置不变

### 示例

```js
数组.sort(function(a,b){
	//升序排列
	//return a-b
	//降序排列
	return b-a
})
```

```js
	const arr = ['March', 'Jan', 'Feb', 'Dec']
	
	// 自定义排序，比较对象的属性值
	arr.sort(function(a,b){
	  return a>b
	})
	
	var friends = [ 
	  {name: 'John', age: 30}, 
	  {name: 'Ana', age: 20}, 
	  {name: 'Chris', age: 25} 
	]; 
	
	function comparePerson(a, b){ 
	  if (a.age < b.age){ 
	    return -1 
	  } 
	  if (a.age > b.age){ 
	    return 1 
	  } 
	  return 0; 
	} 
	
	console.loconst arr = ['March', 'Jan', 'Feb', 'Dec']
	arr.sort(function(a,b){
	  return a>b
	})
	  
	friends.sort(comparePerson); 
```

### 字典序排序

如果没有指明 `compareFunction` ，那么元素会 **按照转换为的字符串** 的诸个字符的 Unicode 位点进行排序。例如 "Banana" 会被排列到 "cherry" 之前。当数字按由小到大排序时，9 出现在 80 之前，但因为（没有指明 `compareFunction`），比较的数字会先被转换为字符串，所以在 Unicode 顺序上 "80" 要比 "9" 要靠前。

如果指明了 `compareFUnction`，要如何还原默认情况下字符串的 Unicode 位点排序呢？

在 JS 中字符串是可以直接比较的，也是按照 Unicode 的位点进行排序

  ```js
  'a'>'b' // flase→0
  'b'>'a' // true→1
  ```

然后因为布尔类型→数值类型的隐式转换，polyfill 如下

  ```js
  const arr = ['March', 'Jan', 'Feb', 'Dec']
  arr.sort(function(a,b){
    return a>b
  })
  ```

### 注意

sort 方法没有归类到遍历方法，一个是因为排序的本质与遍历还是有区别的

## fill()

ES6 新增

`fill` 方法使用给定值，填充一个数组。

```js
['a', 'b', 'c'].fill(7)
// [7, 7, 7]

new Array(3).fill(7)
// [7, 7, 7]
```

上面代码表明，`fill` 方法用于空数组的初始化非常方便。**数组中已有的元素，会被全部抹去。**

可以填充对象，但是会用同一个对象去填充，说白了就是传递了引用，导致修改一个，其他所有被填充的都会一起被修改 @@@

```js
function test(){
  let M = new Array(4).fill([])
  M[0][0] = 1
  console.log(M);
  // 每一个元素都被填充了同一个对象
}
test()
```

# 不影响原数组的方法

## slice()

可以从一个数组中截取指定的元素

**该方法不会影响原数组**，而是将截取到的内容封装为一个新的数组并返回

**参数：**

1. 截取开始位置的索引（包括开始位置）
2. 截取结束位置的索引（**不包括结束位置**）

> 第二个参数可以省略不写，如果不写则一直截取到最后
> 参数可以传递一个负值，如果是负值，则从后往前数。比如：-1，即是倒数第一个

- 如果结束位置大于开始位置 ``arr.slice(2,0)``，则返回空数组 `[]`

**描述**

- 因为索引从 0 开始，而且不包括结束位置，所以限制多少个就输入多少就行，比如截取前 100 个，保留的就是 0-99.所以 slice(0,100) @@@

**polyfill**

```js
  Array.prototype.slice = function(start,end) {
      var result = new Array();
      //没传参数给的默认值
      var start = start || 0;
      var end = end || arr.length;
      for (var i = start; i < end; i++) {
      // this=》当前调用slice的那个数组
          result.push(this[i]);
      }
      return result;
  }
```

## concat()

可以连接两个或多个数组，**它不会影响原数组**，而是新数组作为返回值返回

> 字符串有同名方法，因为性能问题经常使用 '+' 连接符代替，但是在数组中是拼接成新的数组而不是字符串

**参数**

+ `valueN`：任意个数组或者值

> 伪数组会被当成一个值处理

**返回值**: 一个浅拷贝拼接

**示例**

```js
  var alpha = ['a', 'b', 'c'];
  
  var alphaNumeric = alpha.concat(1, [2, 3]);
  
  console.log(alphaNumeric); 
  // results in ['a', 'b', 'c', 1, 2, 3]
```

+ 伪数组被当成一个值处理

```js
  function bar(a,b){
    const arr = [1,2]
    return arr.concat(arguments,[3,4])
  }
  console.log(bar(1,2)) // [1, 2, Arguments(2), 3, 4]
```

## join()

可以将一个数组转换为一个字符串，**不会影响原数组**

**参数：**

- 需要一个字符串作为参数，这个字符串将会作为连接符来连接数组中的元素
- 如果不指定连接符则默认使用逗号，连接

> `Array.prototype.toString()`，就是默认参数的 join 方法

**返回值**: 一个字符串

**描述**

- 一般传递一个空串，可以无缝拼合
- null 和 undefined 会被转换成空串

  ```js
  console.log([null,1,undefined].join()); // ,1,
  ```

**多维数组**

+ 注意连接符与 Array.prototype.toString（）

    ```js
    const abc = [
     [1,5,6], [2,3], [4]
    ];
    console.log(abc.join('-'));  // "1,5,6-2,3-4"
    console.log(abc.join(','));  // "1,5,6,2,3,4"
    ```

## flat() 和 flatMap()

ES6 新增

数组的成员有时还是数组，`Array.prototype.flat()` 用于将嵌套的数组“拉平”，变成一维的数组。该方法返回一个新数组，对原数据没有影响。

```js
[1, 2, [3, 4]].flat()
// [1, 2, 3, 4]
```

上面代码中，原数组的成员里面有一个数组，`flat()` 方法将子数组的成员取出来，添加在原来的位置。

**参数**

- `flat()` 默认只会“拉平”一层，如果想要“拉平”多层的嵌套数组，可以将 `flat()` 方法的参数写成一个整数，表示想要拉平的层数，默认为 1。

  ```js
  [1, 2, [3, [4, 5]]].flat()
  // [1, 2, 3, [4, 5]]
  
  [1, 2, [3, [4, 5]]].flat(2)
  // [1, 2, 3, 4, 5]
  ```

- `flatMap()` 方法对 **原数组** 的每个成员执行一个函数（相当于执行 `Array.prototype.map()`），然后对 **返回值** 组成的数组执行 `flat()` 方法。该方法返回一个新数组，不改变原数组。

  ```js
  // 相当于 [[2, 4], [3, 6], [4, 8]].flat()
  [2, 3, 4].flatMap((x) => [x, x * 2])
  // [2, 4, 3, 6, 4, 8]
  ```

- `flatMap()` 只能展开一层数组。

  ```js
  // 相当于 [[[2]], [[4]], [[6]], [[8]]].flat()
  [1, 2, 3, 4].flatMap(x => [[x * 2]])
  // [[2], [4], [6], [8]]
  ```

- 与其叫 `flatMap()`，其实叫做 `mapFlat(1)` 更加符合实际

# Array Static Method

## Array.isArray()

ES5.1 扩展

**Array.isArray()** 用于确定传递的值是否是一个 [`Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Array)。

```js
Array.isArray([1, 2, 3]);  
// true
Array.isArray({foo: 123}); 
// false
Array.isArray("foobar");   
// false
Array.isArray(undefined);  
// false
```

如果不使用 O.P.toString() 配合 call() 方法，js 缺少一种可以准确判断一个数组的方法，typeof 返回的是 object，原型对象也可以被修改，instanceof 也不一定准确，因此在 ES5 新增了 isArray()

## Array.from()

ES6 扩展

`Array.from` 方法用于将两类对象转为 **真正的数组**：

- 类似数组的对象（array-like object）
- 可遍历（iterable）的对象（包括 ES6 新增的数据结构 Set 和 Map）

**类数组对象**

- 下面是一个类似数组的对象，`Array.from` 将它转为真正的数组。

  ```js
  let arrayLike = {
      '0': 'a',
      '1': 'b',
      '2': 'c',
      length: 3
  };
  
  // ES5的写法
  var arr1 = [].slice.call(arrayLike); // ['a', 'b', 'c']
  
  // ES6的写法
  let arr2 = Array.from(arrayLike); // ['a', 'b', 'c']
  ```

**iterator 数据结构**

- 只要是部署了 Iterator 接口的数据结构，`Array.from` 都能将其转为数组。

  ```js
  Array.from('hello')
  // ['h', 'e', 'l', 'l', 'o']
  
  let namesSet = new Set(['a', 'b'])
  Array.from(namesSet) // ['a', 'b']
  ```

**注意**

- 如果参数是一个真正的数组，`Array.from` 会返回一个一模一样的新数组，浅克隆

  ```js
  Array.from([1, 2, 3])
  // [1, 2, 3]
  ```

- 值得提醒的是，扩展运算符（`...`）也可以将某些数据结构转为数组。
- 扩展运算符背后调用的是遍历器接口（`Symbol.iterator`），如果一个对象没有部署这个接口，就无法转换。 @@@

  > 扩展运算符可以转换 DOMList，DOMList 部署了 iterator 接口，是的，其他原生部署了的请看遍历一章

- `Array.from` 方法还支持类似数组的对象。所谓类似数组的对象，本质特征只有一点，即必须有 `length` 属性。因此，任何有 `length` 属性的对象，都可以通过 `Array.from` 方法转为数组，而此时扩展运算符就无法转换。

  ```js
  Array.from({ length: 3 });
  // [ undefined, undefined, undefined ]
  ```

- 上面代码中，`Array.from` 返回了一个具有三个成员的数组，每个位置的值都是 `undefined`。扩展运算符转换不了这个对象。

**ployfill**

- 对于还没有部署该方法的浏览器，可以用 `Array.prototype.slice` 方法替代。

  ```js
  const toArray = (() =>
    Array.from ? Array.from : obj => [].slice.call(obj)
  )();
  ```

### Array.from() 第二参数

`Array.from` 还可以接受第二个参数，作用类似于数组的 `map` 方法，用来对每个元素进行处理，将处理后的值放入返回的数组。

  ```js
  Array.from(arrayLike, x => x * x);
  // 等同于
  Array.from(arrayLike).map(x => x * x);
  
  Array.from([1, 2, 3], (x) => x * x)
  // [1, 4, 9]
  ```

下面的例子是取出一组 DOM 节点的文本内容。

  ```js
  let spans = document.querySelectorAll('span.name');
  
  // map()
  let names1 = Array.prototype.map.call(spans, s => s.textContent);
  
  // Array.from()
  let names2 = Array.from(spans, s => s.textContent)
  ```

下面的例子将数组中布尔值为 `false` 的成员转为 `0`。

  ```js
  Array.from([1, , 2, , 3], (n) => n || 0)
  // [1, 0, 2, 0, 3]
  ```

另一个例子是返回各种数据的类型。

  ```js
  function typesOf () {
    return Array.from(arguments, value => typeof value)
  }
  typesOf(null, [], NaN)
  // ['object', 'object', 'number']
  ```

### Array.from() 第三个参数

- 如果 `map` 函数里面用到了 `this` 关键字，还可以传入 `Array.from` 的第三个参数，用来绑定 `this`。

### Array.from() 总结

`Array.from()` 可以将各种值转为真正的数组，并且还提供 `map` 功能。这实际上意味着，只要有一个原始的数据结构，你就可以先对它的值进行处理，然后转成规范的数组结构，进而就可以使用数量众多的数组方法。

  ```js
  Array.from({ length: 2 }, () => 'jack')
  // ['jack', 'jack']
  ```

上面代码中，`Array.from` 的第一个参数指定了第二个参数运行的次数。这种特性可以让该方法的用法变得非常灵活。

## Array.of()

ES6 新增

`Array.of` 方法用于将一组值，转换为数组

```js
Array.of(3, 11, 8) // [3,11,8]
Array.of(3) // [3]
Array.of(3).length // 1
```

这个方法的主要目的，是弥补数组构造函数 `Array()` 的不足。因为参数个数的不同，会导致 `Array()` 的行为有差异。

```js
Array() // []
Array(3) // [, , ,]
Array(3, 11, 8) // [3, 11, 8]
```

`Array.of` 基本上可以用来替代 `Array()` 或 `new Array()`，并且不存在由于参数不同而导致的重载。它的行为非常统一。

```js
Array.of() // []
Array.of(undefined) // [undefined]
Array.of(1) // [1]
Array.of(1, 2) // [1, 2]
```

**polyfill**

- `Array.of` 方法可以用下面的代码模拟实现。

  ```js
  function ArrayOf(){
    return [].slice.call(arguments);
  }
  ```

# 数组的搜索

## indexOf()

## lastIndexOf()

从一个字符串中 **检索指定内容**

**参数**

- taget：需要一个字符串作为参数，这个字符串就是要检索的内容
- **start**：可以指定一个第二个参数，来表示 **开始查找的位置**，包括 start

**返回值**

- 如果找到该内容，则会返回其 **第一次** 出现的索引
- 如果没有找到则返回 -1。

**描述**

- indexOf() 是从前向后找
- lastIndexOf() 是从后向前找

**示例**

+ 由于第二个参数 **包括 start 本身**

  ```js
  [0,1,2].indexOf(1,1) // true
  // 从下一个开始查找
  const anotherIndex = nums.indexOf(another,index+1)
  ```

**注意**

- 比较时使用严格运算符，无法发现 NaN，该缺陷由 ES6 Object.is() 弥补，配合 findIndex() 即可

## find() 和 findIndex()

数组实例的 `find` 方法，用于找出第一个符合条件的数组成员。

**参数**

- 接受一个回调函数，接受三个参数
  1. currentValue：正在遍历的元素
  2. index：正在遍历元素的索引
  3. array：正在操作的数组。

  + **需要指定返回值**，类型是布尔类型

- 接受第二个参数 **thisArg**
  - 执行 callback 时，使用的 this 值

**返回值**

- 所有数组成员依次执行该回调函数，找出 **第一个** 返回值为 `true` 的成员，然后 **返回该成员**。如果没有符合条件的成员，则返回 `undefined`。

**示例**

```js
  [1, 4, -5, 10].find((n) => n < 0)
  // -5
  
  [1, 5, 10, 15].find(function(value, index, arr) {
    return value > 9;
  }) // 10
  ```

- 数组实例的 `findIndex` 方法的用法与 `find` 方法非常类似，返回第一个符合条件的 **数组成员的位置**，如果所有成员都不符合条件，则返回 `-1`。

  ```js
  [1, 5, 10, 15].findIndex(function(value, index, arr) {
    return value > 9;
  }) // 2
  ```

- `indexOf` 方法无法识别数组的 `NaN` 成员，但是 `findIndex` 方法可以借助 `Object.is` 方法做到。

  ```js
  [NaN].indexOf(NaN)
  // -1
  
  [NaN].findIndex(y => Object.is(NaN, y))
  // 0
  ```

## includes()

ES7 新增

`Array.prototype.includes` 方法返回一个布尔值，表示某个数组是否包含给定的值，与字符串的 `includes` 方法类似

同样是，采用同值算法

```js
[1, 2, 3].includes(2)     // true
[1, 2, 3].includes(4)     // false
[1, 2, NaN].includes(NaN) // true
```

如果给 includes 方法传入一个起始索引，搜索会从索引指定的位置开始：

```js
let numbers2 = [7, 6, 5, 4, 3, 2, 1]; 
console.log(numbers2.includes(4, 5));  // false
```

`includes()` 方法有意设计为通用方法。它不要求 `this` 值是数组对象，所以它可以被用于其他类型的对象 (比如类数组对象)。下面的例子展示了 在函数的 [arguments](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/arguments) 对象上调用的 `includes()` 方法。

```js
(function() {
  console.log([].includes.call(arguments, 'a')); // true
  console.log([].includes.call(arguments, 'd')); // false
})('a','b','c');
```

## 总结

查找包含某个元素的索引使用 indexOf()

查找是否包含某个元素，使用 includes() 方法

查找符合条件的某个元素的索引，使用 findIndex() 方法

查找符合条件的某个元素，使用 find() 方法

更加通用的方法是 hash-table，可以适用于各种统计、查找的情况

# 数组的遍历

## For 循环

```js
for(var i=0 ; i<数组.length ; i++){//数组[i] }
//其实i就是index的缩写，这个i就是用来遍历数组的  
```

## for...of

![for...of](es-next-2.md#数组)

## forEach()

使用 `forEach()` 方法来遍历数组（不兼容 IE8），数组中有几个元素，回调函数就会被调用几次，每次调用时，都会将遍历到的信息以实参的形式传递进来我们可以定义形参来获取这些信息。

`forEach() ` 方法对数组的每个元素执行一次提供的函数。

### 参数

callback：为数组中每个元素执行的函数，该函数接收三个参数：

1. currentValue：正在遍历的元素
2. index：正在遍历元素的索引
3. array：`forEach()` 方法正在操作的数组。
4. cb 返回值：无意义

thisArg

- 执行 callback 时，使用的 this 值
- 如果 `thisArg` 参数有值，则每次 `callback` 函数被调用的时候，`this` 都会指向 `thisArg` 参数上的这个对象。如果省略了 `thisArg ` 参数,或者赋值为 `null` 或 `undefined`，则 this 指向全局对象

### 返回值

undefined

### 示例

```js
  array.forEach(function(value , index , obj){ 
  
  });
```

## Array.prototype.entries()，keys() 和 values()

ES6 提供三个新的方法——`entries()`，`keys()` 和 `values()`——用于遍历数组。它们都返回一个遍历器对象（详见《Iterator》一章），可以用 `for...of` 循环进行遍历，唯一的区别是 `keys()` 是对键名的遍历、`values()` 是对键值的遍历，`entries()` 是对键值对的遍历。

## map()

以下 API 均是 ES5.1 新增

`map()` 方法创建一个新数组，其结果是该数组中的每个元素都调用一个提供的函数后返回的结果。

**参数**

- **callback**，生成新数组元素的函数，使用三个参数（都是自动传入的，声明并使用即可）：
  1. currentValue，callback 数组当前正在使用的元素
  2. index，当前正在处理的元素的索引
  3. array，正在调用 map 方法的数组
  4. return，返回值会 push 进结果数组，可以是任何类型，没有指定 return 则返回 undefined
- **thisArg**
  - 同 forEach()

**返回值**: 一个新数组，每个元素都是回调函数的结果。

**描述**

- `map` 方法会给原数组中的每个元素都按顺序调用一次 `callback` 函数。`callback` 每次执行后的返回值（包括 [`undefined`]）组合起来形成一个 **新数组**。
- `map ` 不修改调用它的原数组本身（当然可以在 `callback` 执行时改变原数组），需要 **显式 return 并接受返回值**
- **注意**: flatMap()，Array.from(arrayLike,map())，都内置了 map 功能

**示例**

- 用箭头函数简化

  ```js
  const arr = [1,2,3,4,5,6,]
  /*const arr2 = arr.map((item) => {
    return item+10
  })*/
  
  const arr2 = arr.map((item) => {item+10})
  console.log(arr,arr2)
  
  // Array(6) [1, 2, 3, 4, 5, 6]
  // Array(6) [11, 12, 13, 14, 15, 16]


```

**注意**

- 回调函数的三个参数都是 **自动传入** 的

```js
  const numbers = [1, 4, 9];
  const roots = numbers.map(Math.sqrt);
  // Math.sqrt()也传入了value、index、array，只是方法本身只接受一个参数，所以ok
  // roots的值为[1, 2, 3], numbers的值仍为[1, 4, 9]
```

```js
  ["1", "2", "3"].map(parseInt)
  // 实际的结果是 [1, NaN, NaN]
  // 实际的操作是：parseInt('1',0) parseInt('2',1) parseInt('3',2)
  
  ['1', '2', '3'].map( str => parseInt(str) );
  // 一个更简单的方式:
  ['1', '2', '3'].map(Number); // [1, 2, 3]
  // 与`parseInt` 不同，下面的结果会返回浮点数或指数:
  ['1.1', '2.2e2', '3e300'].map(Number); // [1.1, 220, 3e+300]
```

**polyfill**

```js
  // 实现 ECMA-262, Edition 5, 15.4.4.19
  // 参考: http://es5.github.com/#x15.4.4.19
  if (!Array.prototype.map) {
    Array.prototype.map = function(callback, thisArg) {
      var T, A, k;
      if (this == null) {
        throw new TypeError(" this is null or not defined");
      }
      // 1. 将O赋值为调用map方法的数组.
      var O = Object(this);
      // 2.将len赋值为数组O的长度.
      var len = O.length >>> 0;
      // 3.如果callback不是函数,则抛出TypeError异常.
      if (Object.prototype.toString.call(callback) != "[object Function]") {
        throw new TypeError(callback + " is not a function");
      }
      // 4. 如果参数thisArg有值,则将T赋值为thisArg;否则T为undefined.
      if (thisArg) {
        T = thisArg;
      }
      // 5. 创建新数组A,长度为原数组O长度len
      A = new Array(len);
      // 6. 将k赋值为0
      k = 0;
      // 7. 当 k < len 时,执行循环.
      while(k < len) {
        var kValue, mappedValue;
        //遍历O,k为原数组索引
        if (k in O) {
          //kValue为索引k对应的值.
          kValue = O[ k ];
          // 执行callback,this指向T,参数有三个.分别是kValue:值,k:索引,O:原数组.
          mappedValue = callback.call(T, kValue, k, O);
          // 返回值添加到新数组A中.
          A[ k ] = mappedValue;
        }
        // k自增1
        k++;
      }
      // 8. 返回新数组A
      return A;
    };      
  }
  ```

## filter()

**参数**

- 和 map() 类似，自动传入三个参数
  - return：布尔类型，决定当前元素是否 push 进入结果数组
- 设置 thisArg

**返回值**

+ 所有使得 `callback` 返回 true 或 等价于 true 的值的元素组成的一个新数组

**描述**

- `filter` 为数组中的每个元素调用一次 `callback` 函数，**并利用所有使得 `callback` 返回 true 或 等价于 true 的值的元素创建一个新数组。**@
- `filter` 同样不会改变原数组

```js
  function isBigEnough(element) {
    return element >= 10;
  }
  var filtered = [12, 5, 8, 130, 44].filter(isBigEnough);
  // filtered is [12, 130, 44]
  
  ```

## map() 和 filter() 配合

map 没有过滤的功能，不符合条件默认返回的 undefined 也会加入数组

filter 没有 map 的功能，返回的 map 后的元素，也只会被转换为 true

因此必须两者配合才能做到灵活的控制数组

```js
const arr = [1,2,3]
const newArr = arr.filter((val) => {
  if(val<3) return val
}).map((val) => {
  return val*2
})

console.log(newArr);
```

## reduce()

- `reduce()` 方法对数组中的每个元素执行一个由您提供的 **reducer** 函数 (升序执行)，将其结果汇总为单个返回值。

### 参数

callback：**reducer 函数**，接受 4 个参数

  1. Accumulator (acc) (累计器)
  2. currentValue，callback 数组当前正在使用的元素
  3. index，当前正在处理的元素的索引
  4. array，正在调用 map 方法的数组
  5. return：返回值会赋值给累加器, 该返回值在数组的每个迭代中被记住，并最后成为最终的单个结果值。

initialValue：可选

  - 作为第一次调用 `callback函数时` 的第一个参数的值。 如果没有提供初始值，则将使用数组中的第一个元素。 在没有初始值的空数组上调用 reduce 将报错。

唯一一个不可以指定 thisArg 的方法

### 返回值

累加器的最终结果

```js
  members.forEach((member, i) => {
    const membersTag = records.reduce((acc, record) => {
      const { date, tags } = record.get()
      acc.date = date
      acc[member] = tags[i]
      return acc
    }, {})

    res = membersTag
  })
```

### 描述

回调函数第一次执行时，`accumulator` 和 `currentValue` 的取值有两种情况：

- 如果调用 `reduce()` 时提供了 `initialValue`，`accumulator` 取值为 `initialValue`，`currentValue` 取数组中的第一个值；
- 如果没有提供 `initialValue`，那么 `accumulator` 取数组中的第一个值，`currentValue` 取数组中的第二个值。

```js
  const total = [ 0, 1, 2, 3 ].reduce(
    ( acc, cur ) => acc + cur,
    0
  );
  // 进行累加操作，一定要提供初始值，任意值即可
```

### 注意

虽然 reduce 的回调函数, 第一个参数命名是累加器, 但是可以不将其是为累加器，而是仅仅是为一个容器

```js
  var arr = [6, 4, 1, 8, 2, 11, 23];
  
  function max(prev, next) {
      return Math.max(prev, next);
  }
  console.log(arr.reduce(max));
```

把 acc 视为一个容器，用来保存更大的值，循环进行比较，找出最值

### Ployfill

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce

## some()

`some()` 方法测试是否至少有一个元素通过由提供的函数实现的测试。

**参数**

- **callback**: 用来测试每个元素的函数，接受三个参数：
  - valuje
  - index
  - arrya
- **thisArg**: 指定执行 callback 时的 this 值

**返回值**

- 如果回调函数返回任何数组元素的 [truthy](https://developer.mozilla.org/en-US/docs/Glossary/truthy) 值，则返回 **true**；否则为 **false**。

**描述**

- `some()` 被调用时不会改变数组。
- `some()` 遍历的元素的范围在第一次调用 `callback`. 时就已经确定了。在调用 `some()` 后被添加到数组中的值不会被 `callback` 访问到。如果数组中存在且还未被访问到的元素被 `callback` 改变了，则其传递给 `callback` 的值是 `some()` 访问到它那一刻的值。

**示例**

```js
  function isBiggerThan10(element, index, array) {
    return element > 10;
  }
  
  [2, 5, 8, 1, 4].some(isBiggerThan10);  // false
  [12, 5, 8, 1, 4].some(isBiggerThan10); // true
  ```

**polyfill**

- https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/some

## every()

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/every

## 数组遍历方法的共性

遍历的范围在第一次调用 `callback` 前就会确定。在执行过程中新增的元素不会被访问到：

- 防止无限循环，在 callback 里添加元素，新添加的元素又执行 callback
- **换句话说：在使用 for 循环时要防止这种情况出现**
- 使用影响数组长度的方法, 要预先保存数组的长度，否则容易造成无线循环或者过早退出@@@

```js
const arr = [1,2,3,4,5]
arr.forEach(function(value,index,array){
  array.push(value)
})
console.log(arr) // [1,2,3,4,5,1,2,3,4,5]
```

删除元素会减少遍历次数

```js
let testArr = [1,2,3,4,5,6,7]
testArr.forEach((val,i,arr) => {
  arr.pop()
  console.log('1') // 打印4次
})
console.log(testArr) // Array(3) [1, 2, 3]
```

被改变的元素是遍历到他们的时刻的值

```js
const arr = [1,2,3,4,5]
arr.length = 10
arr.forEach(function(value,index,array){
  array[4] = 10
  console.log(value) // 1 2 3 4 10
})
```

`callback` 函数只会在有值的索引上被调用；那些从来没被赋过值或者使用 `delete` 删除的索引则不会被调用。 @@@

```js
const arr = [1,2,3,4,5]
arr.length = 10
arr.forEach(function(value,index,array){
  console.log(index) // 0 1 2 3 4
})

const arr = [1,2,3,4,5]
arr.length = 10
arr.forEach(function(value,index,array){
  array.splice(4,1)
  console.log('invoked') // invoked 只会打印4次
})
```

reduce 唯一一个不可以指定 thisArg 的方法

## 跳出遍历

没有办法中止或者跳出 `forEach()` 循环，除了抛出一个异常。如果你需要这样，使用 `forEach()` 方法是错误的。

若你需要提前终止循环，你可以使用：

- 简单循环
- [for...of](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for...of) 循环
- [`Array.prototype.every()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/every)
- [`Array.prototype.some()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/some)
- [`Array.prototype.find()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/find)
- [`Array.prototype.findIndex()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/findIndex)

这些数组方法可以对数组元素判断，以便确定是否需要继续遍历：[`every()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/every)，[`some()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/some)，[`find()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/find)，[`findIndex()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/findIndex)

若条件允许，也可以使用 [`filter()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) 提前过滤出需要遍历的部分，再用 `forEach()` 处理。

## 回调函数与返回值

forEach()，回调函数的返回值没有意义，方法本身返回 `undefined`

map()，回调函数的返回值会 push 进入一个数组，方法本身返回结果数组

filter，回调函数的返回值是一个布尔值，决定当前遍历的元素是否 push 进结果数组

reduce，回调函数的返回值会被赋值给 acc，如果没有返回值则 acc 被赋值为 undefined，要注意@@@

find，回调函数的返回值是一个布尔值，方法本身返回 **第一个** 回调函数的返回值为 `true` 的成员

some，回函数的返回值是一个布尔值，如果回调函数返回任何数组元素的 [truthy](https://developer.mozilla.org/en-US/docs/Glossary/truthy) 值，则返回 **true**；否则为 **false**。

## 性能

for 循环和其他方法的区别就在于创建了一个匿名函数，这一部分是一个性能开销，会造成 gc，所以 for 循环的性能是更好的

在某些语言中，foreach、map 这种方法可以并行执行，但是 for 循环则无法做到

# 类型数组

JavaScript 数组不是强类型的，因此它可以存储任意类型的数据。

而类型数组则用于存储单一类型的数据。

```js
let myArray = new TypedArray (length)
// 其中TypedArray需替换为下表所列之一。 
```

| 类型数组           | 数据类型           |
| ------------------ | ------------------ |
| Int8Array          | 8 位二进制补码整数  |
| Uint8Array         | 8 位无符号整数      |
| Uint8Clamped Array | 8 位无符号整数      |
| Int16Array         | 16 位二进制补码整数 |
| Uint16Array        | 16 位无符号整数     |
| Int32Array         | 32 位二进制补码整数 |
| Uint32Array        | 32 位无符号整数     |
| Float32Array       | 32 位 IEEE 浮点数     |
| Float64Array       | 64 位 IEEE 浮点数     |

```js
let length = 5; 
let int16 = new Int16Array(length); 
 
let array16 = []; 
array16.length = length; 
 
for (let i=0; i<length; i++){ 
  int16[i] = i+1; 
} 
console.log(int16); 

```

使用 WebGL API、进行位操作、处理文件和图像时，类型数组都可以大展拳脚。它用起来和

普通数组也毫无二致，本章所学的数组方法和功能都可以用于类型数组。

http://goo.gl/kZBsGx 是一个很好的教程，讲解了如何使用类型数组处理二进制数据，以及它

在实际项目中的应用。

# FAQ

 #faq/js

## 构建多维数组

for 循环

new Array fill 空串 再 map, 因为 new Array 实际上只更新了 length 属性, 没有真正的 index

Array.from() 官方推荐的构建方式, 不光是有 length 属性, 还有真正的 index , 默认值为 undefined, 第二个参数位置还可以直接 map

```js
    const rowRestMatrix = Array.from(
        { length: 9 },
        () => Array.from({ length: 9 }).fill(true)
    )
    const colRestMatrix = Array.from(
        { length: 9 },
        () => Array.from({ length: 9 }).fill(true)
    )
    const nineRestMatrix = Array.from(
        { length: 3 },
        () => Array.from(
            { length: 3 },
            () => Array.from({ length: 9 }).fill(true)
        )
    [)]
```

![es-array](programming/font-end/primitive/es/es-array.md#Array%20from%20第二参数)

## 伪数组转换为真数组

```js
let arrayLike = {
    '0': 'a',
    '1': 'b',
    '2': 'c',
    length: 3
};

// ES5的写法
var arr1 = [].slice.call(arrayLike); // ['a', 'b', 'c']

// ES6的写法
let arr2 = Array.from(arrayLike); // ['a', 'b', 'c']
```

### 扩展运算符

扩展运算符背后调用的是遍历器接口（`Symbol.iterator`），如果一个对象没有部署这个接口，就无法转换。

+ Array
+ Map
+ Set
+ String
+ TypedArray
+ 函数的 arguments 对象
+ NodeList 对象

### Array.from

`Array.from` 方法还支持类似数组的对象。所谓类似数组的对象，本质特征只有一点，即必须有 `length` 属性。因此，任何有 `length` 属性的对象，都可以通过 `Array.from` 方法转为数组，而此时扩展运算符就无法转换。

## 伪数组直接使用真数组的方法

直接使用数组的方法，而不是先转换成真数组再调用方法

```js
Array.prototype.forEach.call(eles,function(item,index){}
[].forEach.call(eles,function(){})
```

## 数组的 Remove 方法，删除指定索引

如果 length 小于数组原本的长度，多余的元素会被删除

```js
// Array Remove - By John Resig (MIT Licensed)
Array.prototype.remove = function(from, to) {
  var rest = this.slice((to || from) + 1 || this.length)
  this.length = from < 0 ? this.length + from : from
  return this.push.apply(this, rest)
}
```

remove(index)

remove(from,to)

splice 是删除个数

如果我们用 `delete arr[index]` 的方式删除了数组元素，则只是将该数组中索引值为 `index` 的元素置为 `undefined` ，该位置上是一个空元素，并且数组长度不会减少。

> 元素没有真正被从数组中删除的原因是 `delete` 操作符的作用更多的是释放内存，而不是删除元素。当不再有对这个值的引用时，内存就会自动释放。

用 filter 筛选返回

## Reduce 扩展功能

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce

## 检查数组是否为空

```ts
if (memberList.length > 0) {
  
}
```

为什么要使用 > 0，而不是直接使用 .length ？ length 的取值明明也只有 0 和 非 0，因为在 js 中 memberList 有可能不是数组

```js
if (Array.isArray(memberList) && memberList.length) {
  
}
```

## 数组扁平化

数组的扁平化，就是将一个嵌套多层的数组 array (嵌套可以是任何层数) 转换为只有一层的数组。

举个例子，假设有个名为 flatten 的函数可以做到数组扁平化，效果就会如下：

```js
var arr = [1, [2, [3, 4]]];
console.log(flatten(arr)) // [1, 2, 3, 4]
```

知道了效果是什么样的了，我们可以去尝试着写这个 flatten 函数了

### 递归

我们最一开始能想到的莫过于循环数组元素，如果还是一个数组，就递归调用该方法：

```js
// 方法 1
var arr = [1, [2, [3, 4]]];

function flatten(arr) {
  const len = arr.length
  let result = [];
  for (let i = 0; i < len; i++) {
    if (Array.isArray(arr[i])) {
      result = result.concat(flatten(arr[i]))
    }
    else {
      result.push(arr[i])
    }
  }
  return result;
}
console.log(flatten(arr))
```

### Reduce Concat

reduce concat

```js
//将二维数组转化为一维
const flattened = [[0, 1], [2, 3], [4, 5]].reduce(
 ( acc, cur ) => acc.concat(cur),
 []
);
// flattened is [0, 1, 2, 3, 4, 5]
```

### toString

如果数组的元素 **都是数字**，那么我们可以考虑使用 toString 方法，因为：

```js
[1, [2, [3, 4]]].toString() // "1,2,3,4"
```

调用 toString 方法，返回了一个逗号分隔的扁平的字符串，这时候我们再 split，然后转成数字不就可以实现扁平化了吗？

```js
// 方法2
var arr = [1, [2, [3, 4]]];

function flatten(arr) {
    return arr.toString().split(',').map(function(item){
        return +item
    })
}

console.log(flatten(arr))
```

然而这种方法使用的场景却非常有限，如果数组是 ` [1, '1', 2, '2'] ` 的话，这种方法就会产生错误的结果。

### 扩展运算符

ES6 增加了扩展运算符，用于取出参数对象的所有可遍历属性，拷贝到当前对象之中：

```js
var arr = [1, [2, [3, 4]]];
console.log([].concat(...arr)); // [1, 2, [3, 4]]
```

我们用这种方法只可以扁平一层，但是顺着这个方法一直思考，我们可以写出这样的方法：

```js
// 方法4
var arr = [1, [2, [3, 4]]];

function flatten(arr) {
  while (arr.some(item => Array.isArray(item))) {
    arr = [].concat(...arr);
  }
  return arr;
}

console.log(flatten(arr))
```

### Undercore 递归实现

这里的 flatten 函数并不是最终的 `_.flatten`，为了方便多个 API 进行调用，这里对扁平进行了更多的配置。

```js
/**
 * @param  {Array} input   要处理的数组
 * @param  {boolean} shallow 是否只扁平一层
 * @param  {boolean} strict  是否严格处理元素，下面有解释
 * @param  {Array} output  这是为了方便递归而传递的参数
 */
function flatten(input,{shallow=false,strict=false}={}, output=[]) {
  // 递归使用的时候会用到output
  const len = input.length
  for (let i = 0; i < len; i++) {
    const value = input[i];
    // 如果是数组，就进行处理
    if (Array.isArray(value)) {
      if (shallow) {
        // 如果是只扁平一层，遍历该数组，依此填入 output
        let j = 0, length = value.length;
        while (j < length) output.push(value[j++]);
      }else {
        // 如果是全部扁平就递归，传入已经处理的 output，递归中接着处理 output
        flatten(value,{shallow,strict}, output);
      }
    }else if (!strict){
      // 不是数组，根据 strict 的值判断是跳过不处理还是放入 output
      output.push(value)
    }
  }
  return output;
}
```

解释下 strict，在代码里我们可以看出，当遍历数组元素时，如果元素不是数组，就会对 strict 取反的结果进行判断，如果设置 strict 为 true，就会跳过不进行任何处理，这意味着可以过滤非数组的元素，举个例子：

```js
var arr = [1, 2, [3, 4]];
console.log(flatten(arr,{shallow:true,strict=true})); // [3, 4]
```

那么设置 strict 到底有什么用呢？不急，我们先看下 shallow 和 strct 各种值对应的结果：

- shallow true + strict false ：正常扁平一层
- shallow false + strict false ：正常扁平所有层
- shallow true + strict true ：去掉非数组元素
- shallow false + strict true ： 返回一个 []

```js
_.flatten = function(array, shallow) {
    return flatten(array,{shallow,strict:false});
};
```

在正常的扁平中，我们并不需要去掉非数组元素。

## 交并补

### \_.Union

接下来是 \_.union

该函数传入多个数组，然后返回传入的数组的并集，

举个例子：

```js
_.union([1, 2, 3], [101, 2, 1, 10], [2, 1]);
=> [1, 2, 3, 101, 10]
```

如果传入的参数并不是数组，就会将该参数跳过：

```js
_.union([1, 2, 3], [101, 2, 1, 10], 4, 5);
=> [1, 2, 3, 101, 10]
```

为了实现这个效果，我们可以将传入的所有数组扁平化，然后去重，因为只能传入数组，这时候我们直接设置 strict 为 true，就可以跳过传入的非数组的元素。

```js
function unique(array) {
   return Array.from(new Set(array));
}

_.union = function(...args) {
    return unique(flatten(args,{shallow:true,strict:true}));
}
```

### \_.Difference

效果是取出来自 array 数组，并且不存在于多个 other 数组的元素。跟 _.union 一样，都会排除掉不是数组的元素。

```js
_.difference([1, 2, 3, 4, 5], [5, 2, 10], [4], 3);
=> [1, 3]
```

实现方法也很简单，扁平 others 的数组，筛选出 array 中不在扁平化数组中的值：

```js
function difference(array, ...others) {
    others = flatten(rest,{shallow:true,strct:true});
    return array.filter(function(item){
        return rest.indexOf(item) === -1;
    })
}
```

### 使用 Set 实现

```js
let a = new Set([1, 2, 3]);
let b = new Set([4, 3, 2]);

// 并集，两个集合去重
let union = new Set([...a, ...b]);
// Set {1, 2, 3, 4}

// 交集
let intersect = new Set([...a].filter(x => b.has(x)));
// set {2, 3}

// 差集
let difference = new Set([...a].filter(x => !b.has(x)));
// Set {1}
```

## 去重

请给 Array 本地对象增加一个原型方法，它用于**删除数组条目中重复的条目**(可能有多个)，**返回值是一个包含被删除的重复条目的新数组。**

https://juejin.im/post/5949d85f61ff4b006c0de98b#heading-6

https://juejin.im/post/5b0284ac51882542ad774c45

### 双层循环

每一个元素和后面的元素比较，相等则删除并推入 ret

```js
  Array.prototype.distinct = function(){
    let ret = []
    for (let i = 0; i < this.length; i++) {
      for (let j = i+1; j < this.length; j++) {
        if(this[i]===this[j]){
          ret.push(this.splice(j,1)[0]) 
          // 截断之后导致后面的元素前移，结果是：跳过了下一个元素的判断，导致无法正确去重
          // 如果修改了原数组，则当轮的索引不递增
          j--
        }
      }
    }
    return ret
  }
  
  console.log([7,7,1,1,2,3,4,4,4,5,5,5].distinct())
  console.log(null === null)
```

方案一优化：使用 lastIndexOf() 降低平均时间复杂度

```js
  Array.prototype.distinct = function(){
    let ret = []
    for (let i = 0; i < this.length; i++) {
      if(this.lastIndexOf(this[i]) !== i){
        // 从最后开始找齐，如果索引不相同说明有重复的元素
        ret.push(this.splice(i,1)[0])
        // 删除了元素，索引不递增
        i--
      }
    }
    return ret
  }
  
  // test
  const obj = {}
  console.log([1,1,1,'a','a','a',null,null,{},{},obj,obj].distinct())
```

若返回的重复数组也需要去重，则利用 indexOf 判断是否已经存在，再推入

优点：兼容性好

缺点：两次循环，复杂度为 $O(n^{2})$

### 双层循环改

方案一优化：转换思路，以小比大，降低平均时间复杂度

```js
  const arr = [5,1,1,1,2,4,5,2,2,2,2]
  function unique(array) {
      // ret用来存储结果
      var ret = [];
      // 用于储存重复的结果
      const repeat = []
      for (var i = 0; i < array.length; i++) {
          for (var j = 0,retLen=ret.length; j <retLen ; j++ ) {
              // 如果目标数组有和去重数组相同的元素，跳出当前循环
              if (array[i] === ret[j]) {
                // 没必要动原数组了，因为已经有ret另作保存
                repeat.push(array[i])
                break;
              }
          }
          // 里层循环结束，有两种可能
          // 如果array[i]的元素已经出现再去重数组里了，发生了break，此时j<retLen
          // 如果array[i]的元素不在去重数组里面，那么执行完循环，j 等于 retLen ，说明元素是首次出现或者是唯一的，推入去重数组
          if (j === retLen) {
              ret.push(array[i])
          }
      }
      return {ret,repeat};
  }
  
  console.log(unique(arr).ret); // [1, "1"]
```

方案二优化：同样可以使用 indexOf() 进一步降低时间复杂度

```js
  const arr = [5,1,1,1,2,4,5,2,2,2,2]
  function unique(array) {
    // ret用来存储结果
    var ret = [];
    // 用于储存重复的结果
    const repeat = []
    for (var i = 0; i < array.length; i++) {
      const current = array[i]
      if (ret.indexOf(current) === -1) {
        // 去重数组里没有该元素，说明是第一次出现或者是唯一的，推入去重数组
        ret.push(current)
      }else{
        // 去重数组里已经有该元素了，说明是重复的元素
        repeat.push(current)
      }
    }
    return {ret,repeat};
  }
  
  console.log(unique(arr).ret,unique(arr).repeat); // [1, "1"]
```

优点：兼容性好

缺点：缺点：两次循环，复杂度为 $O(n^{2})$

### 排序后去重

我们先将要去重的数组使用 sort 方法排序后，相同的值就会被排在一起，然后我们就可以只判断当前元素与上一个元素是否相同，相同就说明重复，不相同就添加进 ret

```js
  const arr = [5,1,1,1,2,4,5,2,2,2,2]
  
  function unique(array) {
      var res = [];
      var repeat = []
      // 取出数组的副本进行排序
      var sortedArray = array.concat().sort();
      var prev;
      for (var i = 0, len = sortedArray.length; i < len; i++) {
          // 如果是第一个元素或者相邻的元素不相同
          if (!i || prev !== sortedArray[i]) {
            res.push(sortedArray[i])
          }else{
            repeat.push(sortedArray[i])
          }
          prev = sortedArray[i];
      }
      console.log(repeat)
      return res;
  }
  
  console.log(unique(arr));
  
```

使用 reduce 优化

```js
  // 数组去重
  let arr = [1,2,1,2,3,5,4,5,3,4,4,4,4];
  let result = arr.sort().reduce((init, current)=>{
      if(init.length===0 || init[init.length-1]!==current){
          init.push(current);
      }
      return init;
  }, []);
  console.log(result); //[1,2,3,4,5]
```

优点：排序

缺点：复杂度高，取决于浏览器 sort 方法的实现

### 利用对象属性的唯一性

用一个对象保存出现过的元素，首次出现添加为对象的属性，再次出现则说明重复

```js
  Array.prototype.unique = function () {
    let arr = this;
    let ret = [];
    let temp = {};
    for (let i = 0; i < arr.length; ) {
        // 如果没有这个属性，则添加
        // 要重视语义，长一点又不会死人
        if (!temp.hasOwnProperty([arr[i]]) {
            temp[arr[i]] = 1;
            i++
        }else{
          // 已经有这个属性了，说明是重复的
          ret.push(this.splice(i,1)[0]) 
        }
    }
    return this;
  }
  const obj = {}
  console.log([1,1,1,'a','a','a',null,null,{},{},obj,obj].unique());
```

虽然复杂度降低了，但是

- 但是如果元素是对象，会被认为是同一个属性 `[object Object]` 而发生错误
- 数字 1 和 字符串 1 会被认为是同一个属性

这是因为对象的键值只能是字符串，所以我们可以使用 `typeof item + item` 拼成字符串作为 key 值来避免这个问题

#### 变种

```js
  // 计算数组中每个元素出现的次数
  var names = ['Alice', 'Bob', 'Tiff', 'Bruce', 'Alice'];
  
  var countedNames = names.reduce(function (allNames, name) { 
    if (name in allNames) {
      allNames[name]++;
    }
    else {
      allNames[name] = 1;
    }
    return allNames;
  }, {});
  // countedNames is:
  // { 'Alice': 2, 'Bob': 1, 'Tiff': 1, 'Bruce': 1 }
```

### Map 结构

使用 map 结构优化

```js
  Array.prototype.unique = function () {
    let arr = this;
    let ret = [];
    let map = new Map();
    for (let i = 0; i < arr.length; ) {
        // 如果没有这个属性，则添加
        if (!map.get(arr[i])) {
            map.set(arr[i],1)
            i++
        }else{
          // 已经有这个属性了，说明是重复的
          ret.push(this.splice(i,1)[0]) 
        }
    }
    return this;
  }
  const obj = {}
  console.log([1,1,1,'a','a','a',null,null,{},{},obj,obj].unique());
```

最优方案

### 使用 Filter

ES5 提供了 filter 方法，我们可以用来简化外层循环：

```js
  var array = [1, 2, 1, 1, '1'];
  
  function unique(array) {
      var res = array.filter(function(item, index, array){
          return array.indexOf(item) === index;
      })
      return res;
  }
  
  console.log(unique(array));
```

优点：代码量少

缺点：复杂度并没有改善，无法返回重复的元素

### Set 结构

使用 set 结构，set 结构的去重结果和方案一基本一致

如果返回的数组也需要再次去重，则使用 set 结构即可

向 Set 加入值的时候，**不会发生类型转换**，所以 `5` 和 `"5"` 是两个不同的值。Set 内部判断两个值是否不同，使用的算法叫做“Same-value-zero equality”，它类似于精确相等运算符（`\===`），主要的区别是 `NaN` 等于自身，而精确相等运算符认为 `NaN` 不等于自身。

缺点：怎么返回剔除的重复元素？补集

```js
  // 方法一
  [...new Set(array)]
  // 也可以用于除去重复的字符串
  
  // 方法二
  const s = new Set();
  [2, 3, 5, 4, 5, 2, 2].forEach(x => s.add(x));
  
  // 方法三
  function dedupe(array) {
  	return Array.from(new Set(array));
  }
  
  dedupe([1, 1, 2, 3]) // [1, 2, 3]
```

### 相关 Leetcode 题目

快慢指针跳过重复

| File                                                                       | solution tips overview                |
| -------------------------------------------------------------------------- | ------------------------------------- |
| [[programming/basic/leetcode/剑指 Offer 05. 替换空格\|剑指 Offer 05. 替换空格]]     | [[programming/basic/leetcode/剑指 Offer 05. 替换空格#solution tips\|剑指 Offer 05. 替换空格#solution tips]]   |
| [[programming/basic/leetcode/83. 删除排序链表中的重复元素\|83. 删除排序链表中的重复元素]]       | [[programming/basic/leetcode/83. 删除排序链表中的重复元素#solution tips\|83. 删除排序链表中的重复元素#solution tips]]    |
| [[programming/basic/leetcode/82. 删除排序链表中的重复元素 II\|82. 删除排序链表中的重复元素 II]] | [[programming/basic/leetcode/82. 删除排序链表中的重复元素 II#solution tips\|82. 删除排序链表中的重复元素 II#solution tips]] |
| [[programming/basic/leetcode/80. 删除有序数组中的重复项 II\|80. 删除有序数组中的重复项 II]]   | [[programming/basic/leetcode/80. 删除有序数组中的重复项 II#solution tips\|80. 删除有序数组中的重复项 II#solution tips]]  |
| [[programming/basic/leetcode/26. 删除有序数组中的重复项\|26. 删除有序数组中的重复项]]         | [[programming/basic/leetcode/26. 删除有序数组中的重复项#solution tips\|26. 删除有序数组中的重复项#solution tips]]     |

{ .block-language-dataview}

| File                                                       | solution tips overview        |
| ---------------------------------------------------------- | ----------------------------- |
| [[programming/basic/leetcode/90. 子集 II\|90. 子集 II]]     | [[programming/basic/leetcode/90. 子集 II#solution tips\|90. 子集 II#solution tips]]   |
| [[programming/basic/leetcode/491. 递增子序列\|491. 递增子序列]]   | [[programming/basic/leetcode/491. 递增子序列#solution tips\|491. 递增子序列#solution tips]]  |
| [[programming/basic/leetcode/47. 全排列 II\|47. 全排列 II]]   | [[programming/basic/leetcode/47. 全排列 II#solution tips\|47. 全排列 II#solution tips]]  |
| [[programming/basic/leetcode/40. 组合总和 II\|40. 组合总和 II]] | [[programming/basic/leetcode/40. 组合总和 II#solution tips\|40. 组合总和 II#solution tips]] |
| [[programming/basic/leetcode/18. 四数之和\|18. 四数之和]]       | [[programming/basic/leetcode/18. 四数之和#solution tips\|18. 四数之和#solution tips]]    |
| [[programming/basic/leetcode/15. 三数之和\|15. 三数之和]]       | [[programming/basic/leetcode/15. 三数之和#solution tips\|15. 三数之和#solution tips]]    |

{ .block-language-dataview}

## 最值

配合 Math 的静态方法找出最值，或者使用临时变量保存

### Math.max

1. 如果有任一参数不能被转换为数值，则结果为 NaN。

   ```js
   Math.max(true, 0) // 1
   Math.max(true, '2', null) // 2
   Math.max(1, undefined) // NaN
   Math.max(1, {}) // NaN


```

2. max 是 Math 的静态方法，所以应该像这样使用：Math.max()，而不是作为 Math 实例的方法 (简单的来说，就是不使用 new )
3. 如果没有参数，则结果为 `-Infinity` (注意是负无穷大)，对应的，Math.min 函数，如果没有参数，则结果为 Infinity

   ```js
   var min = Math.min();
   var max = Math.max();
   console.log(min > max);
   ```

### 循环

```js
  var arr = [6, 4, 1, 8, 2, 11, 23];
  
  var result = arr[0];
  for (var i = 1; i < arr.length; i++) {
      result =  Math.max(result, arr[i]);
  }
  console.log(result);
```

### Reduce

```js
  var arr = [6, 4, 1, 8, 2, 11, 23];
  
  function max(prev, next) {
      return Math.max(prev, next);
  }
  console.log(arr.reduce(max));
```

### 排序

如果我们先对数组进行一次排序，那么最大值就是最后一个值：

```js
  var arr = [6, 4, 1, 8, 2, 11, 23];
  
  arr.sort(function(a,b){return a - b;});
  console.log(arr[arr.length - 1])
```

### 找出第二小的元素

+ 排序之后，找到第二小的，O(nlog^n^)
+ 遍历最小的，遍历第二小的 O(2n）
+ 分治算法

### 相关 Leetcode 题目

| File                                                                                                           | solution tips overview                                  |
| -------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------- |
| [[programming/basic/leetcode/628. Maximum Product of Three Numbers\|628. Maximum Product of Three Numbers]] | [[programming/basic/leetcode/628. Maximum Product of Three Numbers#solution tips\|628. Maximum Product of Three Numbers#solution tips]] |
| [[programming/basic/leetcode/414. Third Maximum Number\|414. Third Maximum Number]]                         | [[programming/basic/leetcode/414. Third Maximum Number#solution tips\|414. Third Maximum Number#solution tips]]             |
| [[programming/basic/leetcode/4. 寻找两个正序数组的中位数\|4. 寻找两个正序数组的中位数]]                                             | [[programming/basic/leetcode/4. 寻找两个正序数组的中位数#solution tips\|4. 寻找两个正序数组的中位数#solution tips]]                       |
| [[programming/basic/leetcode/347. 前 K 个高频元素\|347. 前 K 个高频元素]]                                               | [[programming/basic/leetcode/347. 前 K 个高频元素#solution tips\|347. 前 K 个高频元素#solution tips]]                        |
| [[programming/basic/leetcode/230. 二叉搜索树中第K小的元素\|230. 二叉搜索树中第K小的元素]]                                         | [[programming/basic/leetcode/230. 二叉搜索树中第K小的元素#solution tips\|230. 二叉搜索树中第K小的元素#solution tips]]                     |
| [[programming/basic/leetcode/215. 数组中的第K个最大元素\|215. 数组中的第K个最大元素]]                                           | [[programming/basic/leetcode/215. 数组中的第K个最大元素#solution tips\|215. 数组中的第K个最大元素#solution tips]]                      |

{ .block-language-dataview}

## 排序

各种排序算法的变种和应用

| File                                                                                   |
| -------------------------------------------------------------------------------------- |
| [[programming/basic/leetcode/581. 最短无序连续子数组\|581. 最短无序连续子数组]]                       |
| [[programming/basic/leetcode/451. 根据字符出现频率排序\|451. 根据字符出现频率排序]]                     |
| [[programming/basic/leetcode/406. 根据身高重建队列\|406. 根据身高重建队列]]                         |
| [[programming/basic/leetcode/414. Third Maximum Number\|414. Third Maximum Number]] |
| [[programming/basic/leetcode/4. 寻找两个正序数组的中位数\|4. 寻找两个正序数组的中位数]]                     |
| [[programming/basic/leetcode/347. 前 K 个高频元素\|347. 前 K 个高频元素]]                       |
| [[programming/basic/leetcode/274. H-Index\|274. H-Index]]                           |
| [[programming/basic/leetcode/23. 合并 K 个升序链表\|23. 合并 K 个升序链表]]                       |
| [[programming/basic/leetcode/220. 存在重复元素 III\|220. 存在重复元素 III]]                     |
| [[programming/basic/leetcode/21. 合并两个有序链表\|21. 合并两个有序链表]]                           |
| [[programming/basic/leetcode/14. 最长公共前缀\|14. 最长公共前缀]]                               |
| [[programming/basic/leetcode/88. 合并两个有序数组\|88. 合并两个有序数组]]                           |

{ .block-language-dataview}

## Traverse Array

基本上数组相关的题目都是遍历的把，不知道这个 tag 是否有意义

| File                                                                             |
| -------------------------------------------------------------------------------- |
| [[programming/basic/leetcode/598. Range Addition II\|598. Range Addition II]] |
| [[programming/basic/leetcode/495. 提莫攻击\|495. 提莫攻击]]                           |
| [[programming/basic/leetcode/485. 最大连续 1 的个数\|485. 最大连续 1 的个数]]               |
| [[programming/basic/leetcode/396. 旋转函数\|396. 旋转函数]]                           |
| [[programming/basic/leetcode/121. 买卖股票的最佳时机\|121. 买卖股票的最佳时机]]                 |

{ .block-language-dataview}
