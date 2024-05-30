---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2022-09-03-Sat, 10:08:28 pm","date-modified":"2023-04-29-Sat, 8:19:21 pm","permalink":"/programming/back-end/mongo/","dgPassFrontmatter":true}
---


9/27

# mongoDB 基本知识

## 数据库（Database）

- 数据库是按照数据结构来组织、存储和管理数据的仓库。
- 我们的程序都是在内存中运行的，一旦程序运行结束或者计算机断电，程序运行中的数据都会丢失。
- 所以我们就需要将一些程序运行的数据持久化到硬盘之中，以确保数据的安全性。而数据库就是数据持久化的最佳选择。
- 说白了，数据库就是存储数据的仓库。

### 数据库分类

- 数据库主要分成两种：

1. 关系型数据库 (RDBMS)

   - SQL 结构化查询语言，是一门标准的语言，与 JAVA、C、JS
   - MySQL、Oracle、DB2、SQL Server …
   - 关系数据库中全都是表

2. 非关系型数据库 (No SQL Not Only SQL)

   - MongoDB、Redis ……
   - 键值对数据库 其他 No SQL 数据库
   - 文档数据库 MongoDB

## MongoDB 简介

- MongoDB 是为快速开发互联网 Web 应用而设计的数据库系统。
- MongoDB 的设计目标是极简、灵活、作为 Web 应用栈的一部分。
- MongoDB 的数据模型是面向文档的，所谓文档是一种类似于 JSON 的结构，简单理解 MongoDB 这个数据库中存的是各种各样的 JSON。（BSON：增强版 JSON Bintary JSON，除了 JSON，还可以存一些二进制数据）
- 与 NodeJS 类似，对 ES 标准的实现，不支持 BOM\DOM

## 安装 MongoDB

### 完整安装步骤

1. 配置环境变量
2. 指定端口和路径

   - mongod --dbpath C:\Users\lilichao\Desktop\mongo\data\db --port 123
   - 默认端口号：27017
   - ![1547132328933](/img/user/programming/back-end/mongo/1547132328933.png)

3. 打开 cmd 命令行窗口

   - 输入 mongod 启动 mongodb 服务器
   - mongod 用来启动**数据库服务器**
   - 服务器用来保存数据

4. 再打开一个 cmd 窗口

   - 输入 mongo 连接 mongodb，出现 >
   - mongo 用来启动**数据库客户端**
   - 客户端用来操作服务器，对数据进行增删改查的操作

5. 将 MongoDB 设置为系统服务

   - 可以自动在后台启动，不需要每次都手动启动
   - 在 c 盘根目录创建 data
     - 在 data 下创建 db 和 log 文件夹
     - log，日志，启动服务器的时候自动弹出的信息，其实就是日志，设置好后会把日志写到 log 文件夹里
   - 创建配置文件
     - 在目录 C:\Program Files\MongoDB\Server\3.2 下添加一个配置文件 mongod.cfg
   - 以管理员的身份打开命令行窗口
   - 执行如下的命令
     - ```js
       sc.exe create MongoDB binPath= "\"F:\ MongoDB\Server\3.2\bin\mongod.exe\" --service --config=\"F: \MongoDB\Server\3.2\mongod.cfg\"" DisplayName= "MongoDB" start= "auto"
       
       sc.exe create MongoDB binPath= "\"mongod的bin目录\mongod.exe\" --service --config=\"mongo的安装目录\mongod.cfg\"" DisplayName= "MongoDB" start= "auto"
       
       sc.exe create MongoDB binPath= "\"F:\MongoDB\Server\3.2\bin\mongod.exe\" --service -- config=\"F:\MongoDB\Server\3.2\mongod.cfg\"" DisplayName= "MongoDB" start= "auto"
       ```

6. 启动 mongodb 服务

   - 如果启动失败，证明上边的操作有误
   - 在控制台输入 sc delete MongoDB 删除之前配置的服务
   - 然后从第一步再来一次
   - 错误 1053：路径有错误，从 word 复制到 txt 可能加上了一些空格之类的，严格对照

7. 在任务管理器的服务，设置 MongoDB 为自动

### MongoDB 常用指令

![1547132616075](/img/user/programming/back-end/mongo/1547132616075.png)

# 2. 基本概念

## 三个概念

### 数据库（database）

- 数据库是一个仓库，在仓库中可以存放集合。
- 一个服务器可以有多个数据库

### 集合（collection）

- 集合类似于数组，在集合中可以存放文档。

### 文档（document）

- 文档数据库中的最小单位，我们存储和操作的内容都是文档。
- 文档的基本形式是：JSON。每一个 JSON 对象我们都可以称为一个文档。注意属性名有些时候可以不写引号，而{}外的引号省略，不要误以为是 js 对象了，其实是 JSON 对象

![1547132712733](/img/user/programming/back-end/mongo/1547132712733.png)

# 3. 基本指令

- show dbs
- show databases
  - 显示当前的所有数据库
- use 数据库名
  - 进入到指定的数据库中
  - 如果数据库还未被创建，当我们写入文档的同时，会自动创建该数据库，集合也是一样
- db
  - db 表示的是当前所处的数据库
- show collections
  - 显示数据库中所有的集合
- 增 create 删 read 改 update 查 delete

## 插入文档

### db.collection.insert(doc)

- 例子：
- 向 test 数据库中的，stus 集合中插入一个新的学生对象

```js
{name:"孙悟空",age:18,gender:"男"}
db.stus.insert({name:"孙悟空",age:18,gender:"男"})
```

- 显示：![1547175715473](/img/user/programming/back-end/mongo/1547175715473.png)
- new 插入 一个文档

**注意**

- 以数组形式传入多个对象，即可同时插入多个文档
- 当插入文档时，如果没有指定 _id 属性，则数据库会自动添加该属性，用于做该文档的唯一标识，根据时间戳和 MAC 码生成
- 开发环境、生产环境的数据同步需要 _id 属性做判断
- db.stus.insert({_id: "hello”,name:" 孙悟空 ",age:18,gender:" 男 "})

### db.collection.insertOne(doc)

- 向集合中插入一个文档

### db.collection.insertMany(doc)

- 向集合中插入多个文档

## 查询文档

### db.collection.find()

- 可以根据指定条件从集合中查询所有符合条件的文档
- 返回的是一个数组
- 可以传入一个对象，作为查询条件
- db.collection.find()[index]
  - 默认情况下按照 _id 的值排列

### db.collection.find({字段名：值})

- 属性=字段，查询含有对应属性值的文档（**区分数据类型不会自动转换**）
  - objectId 需要先转换类型，才能正确查询
- 值可以是一个数组，只要含有即可，区别于查询操作符 $eq，需要等于
- 传多个条件的时候只有**满足所有条件**才会返回
- `db.numbers.find({num:{$gt:40,$lt:50}});`

### db.collection.findOne()

- 查询**第一个**符合条件的文档
- 返回的**是一个对象**

### db.collection.find().count()

- 查询符合条件的文档的数量
- 有 length 属性，但是有点区别
  - ![1547176185062](/img/user/programming/back-end/mongo/1547176185062.png)
  - 如果调用 find({}).length，性能较差，先获取数组，在输出结果，而 count 直接获取结果

## 修改文档

### db.collection.update(\<query>，\<update>，{options})

![1547176260116](/img/user/programming/back-end/mongo/1547176260116.png)

- 可以修改、替换集合中的一个或多个文档
  - `db.stus.update({name:”沙和尚”}，{age:”28”})`
  - 把沙和尚的年龄改为 28
  - ![1547176349026](/img/user/programming/back-end/mongo/1547176349026.png)
- 默认情况下会用新对象替换原对象
- 使用修改操作符
  - ![1547176423483](/img/user/programming/back-end/mongo/1547176423483.png)
  - $set 可以用来修改文档中的指定属性
  - $unset 用来删除文档的制定属性
  - $push 类数组操作，用于添加元素
  - $addToSet 向集合添加一个新元素
  - $inc 增加指定值的数量 只能是数字？
  - set 还有集合的意思，charset，而且是元素不重复的集合，所以无法添加重复的元素
- `db.stus.update({name:”沙和尚”}，{$set{ age:”28”} })`
- `db.stus.update({name:”沙和尚”}，{$unset{ age:”28”} })`
  - 删除的时候，指定属性的值不重要，随便传
- update 方法，默认情况下只会修改一个，所以修改 options 的 multi 值即可

### db.collection.updateOne(查询条件，新对象)

- 修改集合中的一个文档

### db.collection.updateMany(查询条件，新对象)

- 修改集合中的多个文档

### db.collection.replaceOne()

- 替换集合中的一个文档

## 删除文档

### db.collection.remove()

- 删除集合中的一个或多个文档（默认删除多个）
- 根据条件删除文档，传递的条件和 find() 一样
- ![1547176574158](/img/user/programming/back-end/mongo/1547176574158.png),第二个参数传递一个 true 则只删除一个

### db.collection.deleteOne()

- 删除集合中的一个文档

### db.collection.deleteMany()

- 删除集合中的多个文档

### db.collection.remove({})

- remove() 必须传参数，不然会报错，如果传一个空集
  - 清空一个集合
  - 先匹配再一个个删除，所以性能略差

### db.collection.drop()

- 删除一个集合
- 删除了所有集合，所属数据库也会自己删除，那删除所有文档，集合还在

### db.dropDatabase()

- 删除一个数据库

## 开发实际情况

- 数据是最值钱的，所以一般数据库中的数据很少会删除，一旦删除就再也找不到了。再实际开发中，一般会在数据中添加一个字段（属性）表示是否被删除
- 文档的属性值也可以是一个文档，此时我们称这个文档为内嵌文档
- `db.test.update({name:”孙悟空”}，{$set{hobby:{具体内容}}});`
  - 此处 hobby 即是一个文档
- 要查询内嵌文档的属性可以通过.访问，但是此时属性名必须使用引号
  - `db.test.find({“hobby.movies”:”hero”});`
  - `db.test.update({name:”tangseng”},{$push{“hobby.movies”:”1984”}});`
- 数据库的语法性能不好，能少用尽量少用，多用 es 的语法
  - `var arr = [] ; arr.push(…);db.collections.insert(arr);`

## 查询操作符

- ![1547176718092](/img/user/programming/back-end/mongo/1547176718092.png)

```js
$eq; $ne;$gt; $gte; $lt; $lte; greater than less than equal not equal
$in; Matches any of the values specified in an array.
$nin; Matches none of the values specified in an array.

db.numbers.find(num:{&gt:500})

db.numbers.find({num:{$gt:40,$lt:50}});

db.numbers.find().skip(integer).limit(integer);
```

- sort() 指定排序的规则，需要一个对象来表示排序的规则，1 表示升序，-1 表示降序
- `de.emp.find().sort({sal:1,empno:-1})  `
  - 当 sal 一样的时候，empno 降序排列
- skip() 用于跳过指定数目的数据
- limit() 限制查询返回的指定条数
- MongoDB 会自动调整 sort、skip 和 limit 的位置，所以相对位置没有关系，大于小于也类似
  - 先 sort 再 skip 再 limit

```js
db.emp.find({},{ename:1,_id:0,sal:1}); //只显示ename、sal，_id默认显示，不显示_id，

$or;db.emp.find({$or :[{sal:{$lt:1000}},{sal:{$gt:2500}}]})
```

- ![1547176906238](/img/user/programming/back-end/mongo/1547176906238.png)
  - 索引或者 findOne 才能返回对象

# 4. 文档之间的关系

## 一对一

- one to one 一夫一妻
- 在 MongoDB 中，可以通过内嵌文档的形式来体现一对一的关系

## 一对多

- 多对一 one to many 直属领导和下属 用户 订单
- 也可以通过内嵌文档来体现，把属性变成数组
- 在第二个集合 orders，增加一个 user_id 字段来标志 user，关联两个集合

## 多对多

- many to many
- 分类 商品 一个分类多个商品 一个商品多个分类
- 用 teacher_id 数组来标识老师

# Mongoose 简介

- 之前我们都是通过 shell 来完成对数据库的各种操作的，在开发中大部分时候我们都需要通过程序来完成对数据库的操作。
- 而 Mongoose 就是一个让我们可以通过 Node 来操作 MongoDB 的模块。
- MongoDB 本身就是 Node 的一个模块，Mongoose 让我们操作 MongoDB 模块更加简单
- Mongoose 是一个对象文档模型（ODM:js 对象、数据库文档，以对象的形式操作数据库的文档）库，它对 Node 原生的 MongoDB 模块进行了进一步的优化封装，并提供了更多的功能。
- 在大多数情况下，它被用来把结构化的模式应用到一个 MongoDB 集合，并提供了验证和类型转换等好处

## Mongoose 的好处

- 可以为文档创建一个模式结构（Schema）
- 可以对字段进行检验，数目，数据类型
- 可以对模型中的对象/文档进行验证
- 数据可以通过类型转换转换为对象模型
- 可以使用中间件来应用业务逻辑挂钩
- 比 Node 原生的 MongoDB 驱动更容易

# 2. 基本概念

## 新的对象

### Schema(模式对象)

- Schema 对象定义约束了数据库中的文档结构

### Model

- Model 对象作为集合中的所有文档的表示，相当于 MongoDB 数据库中的**集合 collection**

### Document

- Document 表示集合中的具体文档，相当于集合中的一个具体的文档
- 三者存在先后关系

# 3. Mongoose 的使用

## 链接数据库

1. 安装 Mongoose
   - npm i mongoose –save

2. 引入 Mongoose
   - var mongoose = require(“mongoose”);

3. 链接 MongoDB 数据库

   - `mongoose.connect('mongodb://localhost/test'.{useMongoClient:true});`
   - mongodb://地址: 端口号/数据库名
   - 如果端口号是默认端口号 27017 则可以省略
- 断开连接
  - `mongoose.disconnect()`
  - 一般不需要这个方法，一般只会链接一次，除非项目停止服务器关闭，否则链接不会断开

4. 监听 MongoDB 的链接状态

   - 在 mongoose 对象中，有一个属性叫做 connection，该对象表示的是数据库的链接，通过监视该对象的状态可以监听数据库的断开与链接
   - `mongoose.connection.once(“open”,function(){});`
   - `mongoose.connection.once(“close”,function(){});`

## 创建 Shcema 对象

- 大写的 S，因为是一个构造函数
- `var mongoose = require("mongoose")`
- `var Schema = mongoose.Schema;`
  - 赋值变量，方便以后使用

```js
var stuSchema = new Schema({
  name:{type:String,required: true}
  age:Number,
  gender:{
    type:String,
    default:"female"
  },
  address:String
})
```

### 数据类型，必填，默认值

## 创建 Model 对象

- mongoose.model(ModelName, schema);
  - modelName: 要映射的集合名字
  - mongoose 会自动将单数集合名变成复数
- `var StuModel = mongoose.model('Student', stuSchema); `
  - 大写，因为一个集合，一个构造函数，find() 相当于找这个构造函数的所有实例，数据库角度，找集合中的所有文档

## 创建 Doucument 对象

-       ModelName.create({doc,function(err){});
-       ![1547180761337](mongo/1547180761337.png) 
-       此时数据库、集合才被创建，和原生MongoDB一样
-       如果不传gender值，则默认是female

# 4. 增删改查（Model 方法）

-       有了Model对象，就可以进行增删改查的操作了

## 增

### Model.create(doc(s),[callback])

- 用来创建一个或多个文档并添加到数据库中 //db.collection.insert()
- 参数：

1. doc(s)，可以是一个文档对象，也可以是一个文档对象的数组或多个文档对象
2. callback，可选的，当操作完成之后执行，返回 err 和插入的文档

- ![1547180894405](/img/user/programming/back-end/mongo/1547180894405.png)

## 查

- 通过 find() 方法返回的对象，就是 Document 文档对象 //即是数据库的数据
- Document 对象是 Model 的实例 //instance of
  - ![img](/img/user/programming/back-end/mongo/clip_image002-1547180953267.jpg)，像这种带#的方法是实例的方法
  - find() 相当于找这个构造函数的所有实例，数据库角度，找集合中的所有文档

### Model.find(conditions, [projection\], [options], [callback])

- 文档的 _id 属性，是一个对象，但是以字符串形式也是可以 find 到的，并不影响
- 查询所有符合条件的文档对象**数组**
- conditions: 查询的条件，传一个空对象则查询所有
- projection: 投影
- options: 查询选项（skip limit）
- callback：回调函数，查询结果会通过回调函数返回，以数组形式，数组里面是一个个文档对象
- `StuModel.find({name:”唐僧”},function(err,docs){!err?docs.log→:;})`
  - ![1547186756263](/img/user/programming/back-end/mongo/1547186756263.png)
- `StuModel.find({},{name:1,_id:0},function(err,docs){!err?docs.log→:;})`
  - ![1547186792160](/img/user/programming/back-end/mongo/1547186792160.png) 设置投影，与 MongoDB 一致
- `StuModel.find({},”name age -_id”,function(err,docs){!err?docs.log→:;})`
  - ![1547186824606](/img/user/programming/back-end/mongo/1547186824606.png)字符串形式设置投影，写什么有什么，空格连接，取消默认的 _id 用负号
- **投影用于过滤属性**
- `StuModel.find({},”name age -_id”,{skip:3},function(err,docs){!err?docs.log→:;})`
  - ![1547186871650](/img/user/programming/back-end/mongo/1547186871650.png) 跳过前三个

### Model.findOne(conditions, [projection], [options], [callback]）

- 查询符合条件的一个文档**对象**
- callback:err,doc

### Model.findById(id, [projection], [options], [callback]）

- 根据文档的 ID 查询文档对象
- id 以字符串形式传入
  - ![1547186987610](/img/user/programming/back-end/mongo/1547186987610.png)
  - ![1547186992043](/img/user/programming/back-end/mongo/1547186992043.png)
  - 封装之后直接传（）里的字符串即可

## 改

### Model.update(conditions,doc, [options], [callback]）

### Model.updateOne(conditions,doc, [options], [callback]）

### Model.updateMany(conditions,doc, [options], [callback]）

- 用于修改一个或多个文档对象

1. conditions: 查询条件
2. doc：传入的是需要修改的属性

   - 有这个属性就修改 value，没有这个属性就添加这个属性，**原本对象不会被改写**
   - All top level update keys which are not atomic operation names are treated as set operations
   - 所有不是指定 $级别的操作，都会被视为$set，这是为了防止重写文档对象，所以实验的结果是成立的
   - 传入的文档已经是对象形式了，不要多余的加一个括号，会被认为是参数设置
   - `PostModel.update({_id:postInfo._id},postInfo,{new:true},function(err,post){}`

3. options: 配置参，

   - multi:true ,update 查多个
   - new:true,回调函数的参数是修改后的对象，想要在回调函数内部访问修改后的对象，一个是设置该选项，或者用 assign 合并，因为常常要对 oldDoc 进行判断

4. callback: 回调函数

- `StuModel.updateOne({name:”唐僧”},{$set{age:20}},function(err){!err?“修改成功”.log→:;})`
- update 参数传入 $set，可以修改指定属性，防止把整个对象重写{}，新版本做了相应的修改，现在不传入$set 也可以

### Model.replaceOne(conditions,doc, [options], [callback]）

- 会覆盖

## 删

### Model.remove(conditions, [callback]）

- 已被废黜

### Model.deleteOne(conditions, [callback]）

### Model.deleteMany(conditions, [callback]）.

- `StuModel.remove({name:”白骨精”},function(err){!err?”ok”.log→:;})`
- delete 和 remove 的不统一命名还是存在和原生一样
- { ok: 1, n: 2 }
- ok 为 1 操作成功，n 代表匹配到的文档的数量
- 返回的是被删除的文档

## 其他方法

### Model.count(conditions, [callback]）

- 统计文档的数量
- 如果调用 find({}).length，性能较差，先获取数组，在输出结果，而 count 直接获取结果
- `StuModel.count({},function(err,count{!err?count.log→:;});`

# 5. Ducument 方法

- Document 对象和数据库里的文档一一对应，Document 对象是 Model 的实例，通过 Model 查询到的结果都是 Document
- `var stu = new StuModel({name:,age,address});`
- Model 是构造函数，用构造函数直接创建对象，而 Model.create()，创建之后还会自动添加到 Model

## [Model#save([options\] [options.safe], [options.validateBeforeSave],[fn])

- `stu.save(function(err){!err?”ok”.log→:;});`
  - 自动保存到 StuModel 的集合中

## update(update,[options],[callback])

- Model.update() 要先传条件 conditions
- `doc.update({$set:{age:28}},function(err){ })`
- 或者直接：doc.age = 28 ; doc.save();
- 因为就是一个对象，就是对象的属性

## remove([callback])

- 删除自己
- `doc.remove(function(err){});`

## get(path,[type])

- 获取文档中的指定属性值
- doc.get(“name”);
- doc.name;

## set(path,value,[type])

- doc.set(“name”,”猪小小”);
- doc.name = “猪小小”;
- doc._id/doc.id

## equals(doc)

- 是否是同一个文档

## isNew

- 是否是新对象，有没有存进数据库，存了 false，没存 new

## toJSON()

## toObject()

- 将 Document 对象转换为普通 JS 对象
- 转换为普通 JS 对象之后，所有的 Document 对象的方法或属性不能再使用
- 作用：数据库保留隐私数据，但是前端显示的时候临时把隐私数据删除![img](/img/user/programming/back-end/mongo/clip_image002-1547187742778.jpg)，如果是 Document 对象无法用 delete 操作符删除属性，如果用 $unset 则会删除数据库的数据，后患无穷
- doc.id //undefined doc._id //因为是普通 JS 对象了，不会自动转化
- ![1547187917067](/img/user/programming/back-end/mongo/1547187917067.png)

# 6. Mongoose 模块化

- 引入 Mongoose，连接数据库，设定 Schema 在开发中中需要反复使用，每次不同的 js 文件都要重新引入，我们实际再操作的主要是 Model 和 Document，所以把这些部分模块化
- 定义一个 js 文件专门用于连接数据库

## 连接数据库的模块

```js
/*连接数据库*/
// 引入mongoose
const mongoose = require('mongoose')
// 连接指定数据库(URL只有数据库是变化的)
mongoose.connect('mongodb://localhost:27017/calender',{ useNewUrlParser: true })
// 获取连接对象
const conn = mongoose.connection
// 绑定连接完成的监听(用来提示连接成功)
conn.once('connected', () => {
  console.log('db connect success!')
```

## 定义 Schema 的模块

```js
/* 定义出对应特定集合的Model并向外暴露*/
// 引入mongoose
const mongoose = require('mongoose')

const Schema = mongoose.Schema
// 定义userSchema(描述文档结构)
const userSchema = new Schema({
  events:{type:Array} //储存着用户创建的events的_id
})
// 定义Model(与集合对应, 可以操作集合)
const UserModel = mongoose.model('user', userSchema) // 集合为: users
// 向外暴露Model
exports.UserModel = UserModel
```

- ![1547188225341](/img/user/programming/back-end/mongo/1547188225341.png)
- ![1547188251888](/img/user/programming/back-end/mongo/1547188251888.png) 此时返回的，就是 StuModel 模型对象

##
