---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2020-09-03-Thu, 3:23:26 pm","date-modified":"2023-04-29-Sat, 8:20:18 pm","permalink":"/programming/back-end/express/","dgPassFrontmatter":true}
---


# Express

## Express 应用程序生成器

通过应用生成器工具 `express-generator` 可以快速创建一个应用的骨架。

`express-generator` 包含了 `express` 命令行工具。通过如下命令即可安装：

`npm install express-generator -g`

```sh
$ express -h

  Usage: express [options] [dir]

  Options:

    -h, --help          输出使用方法
        --version       输出版本号
    -e, --ejs           添加对 ejs 模板引擎的支持
        --hbs           添加对 handlebars 模板引擎的支持
        --pug           添加对 pug 模板引擎的支持
    -H, --hogan         添加对 hogan.js 模板引擎的支持
        --no-view       创建不带视图引擎的项目
    -v, --view <engine> 添加对视图引擎（view） <engine> 的支持 (ejs|hbs|hjs|jade|pug|twig|vash) （默认是 jade 模板引擎）
    -c, --css <engine>  添加样式表引擎 <engine> 的支持 (less|stylus|compass|sass) （默认是普通的 css 文件）
        --git           添加 .gitignore
    -f, --force         强制在非空目录下创建
```

例如，如下命令创建了一个名称为 *myapp* 的 Express 应用。此应用将在当前目录下的 *myapp* 目录中创建，并且设置为使用 [Pug](https://pugjs.org/) 模板引擎（view engine）：

```sh
express --view=pug myapp
```

`npm install` 安装所有依赖

生成的目录结构

```sh
.
├── app.js
├── bin
│   └── www //修改端口号
├── package.json
├── public
│   ├── images
│   ├── javascripts
│   └── stylesheets
│       └── style.css
├── routes
│   ├── index.js // 注册路由
│   └── users.js
└── views
    ├── error.pug
    ├── index.pug
    └── layout.pug
```

## Live Reload

问题: 每次修改后台应用代码, 需要重新运行命令修改才生效

解决: 使用 nodemon 包

- monitor，监视器

下载: `npm install --save-dev nodemon`

- `package.json` 配置:` "start": "nodemon ./bin/www"`

测试: 修改后台任何代码, 会自动重新运行最新的代码 (按下 ctrl + S)

## 使用路由

### API

Routing refers to determining how an application responds to a client request to a particular endpoint, which is a URI (or path) and a specific HTTP request method (GET, POST, and so on).

Each route can have one or more handler functions, which are executed when the route is matched.

路由的定义方法如下

```js
app.METHOD(PATH, HANDLER)
```

- `app` is an instance of `express`.
- `METHOD` is an [HTTP request method](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods), in lowercase.
- `PATH` is a path on the server.
- `HANDLER` is the function executed when the route is matched.

The following examples illustrate defining simple routes.Respond with `Hello World!` on the homepage:

```js
app.get('/', function (req, res) {
  res.send('Hello World!')
})
```

Respond to POST request on the root route (`/`), the application’s home page:

```js
app.post('/', function (req, res) {
  res.send('Got a POST request')
})
```

### 实例：用户注册的接口

关于 code 0 1 的讨论

https://segmentfault.com/q/1010000015622870/a-1020000015622950

```js
router.post('/register', function (req, res, next) {
  //1.获取参数
  //post请求参数放在req的body属性中
  //get请求，query参数和param参数，req.query || req.param
  const {username, password} = req.body
  //2.处理数据
  console.log('register', username, password)
  if (username === 'admin') {
    //3.返回响应数据（对象）
    res.send({code: 1, msg: '此用户已存在'})
  } else {
    res.send({code: 0, data: {_id: 'abc', username, password}})
  }
})
```

## 使用 Mongoose 操作数据库

下载依赖包

`npm install --save mongoose blueimp-md5`

`db/db_test.js`

### 链接数据库

```js
//commonJS语法，因为mongoose
const md5 = require('blueimp-md5')
// 1. 连接数据库
// 1.1. 引入 mongoose
const mongoose = require('mongoose')
// 1.2. 连接指定数据库(URL 只有数据库是变化的)
mongoose.connect('mongodb://localhost:27017/gzhipin_test2')
// 1.3. 获取连接对象
const conn = mongoose.connection
// 1.4. 绑定连接完成的监听(用来提示连接成功)
conn.on('connected', function () {
  console.log('数据库连接成功')
})
```

右键 run 进行测试

或运行命令： `node db/db_test.js`

### 得到对应特定集合的 Model

用于操作集合的数据

定义 Schema，保存 Model 构造函数

向外暴露 Model

```js
// 2. 得到对应特定集合的 Model
// 2.1. 字义 Schema(描述/定义文档结构)
//属性名、属性值的的类型、是否是必须的、默认值
const userSchema = mongoose.Schema({
  username: {type: String, required: true}, // 用户名
  password: {type: String, required: true}, // 密码
  type: {type: String, required: true}, // 用户类型: dashen/laoban
})
// 2.2. 定义 Model(与集合对应, 可以操作集合)，返回一个构造函数
const UserModel = mongoose.model('user', userSchema)
// 之后设定集合名为: users，一一对应
// 2.3. 向外暴露 Model
exports.UserModel = UserModel

```

### 通过 Model 或者其实例进行 CRUD 的操作

C：SAVE

```js
// 3.1. 通过 Model 实例的 save()添加数据
function testSave() {
// user 数据对象
  const user = {
    username: 'xfzhang',
    password: md5('1234'),
    type: 'dashen',
  }
  const userModel = new UserModel(user)
// 调用实例的save方法保存到数据库
  userModel.save(function (err, userDoc) {
    console.log('save', err, userDoc)
  })
}
//testSave()

```

R：Retrieve

```js
// 3.2. 通过 Model 的 find()/findOne()查询多个或一个数据
function testFind() {
// 查找多个
  UserModel.find(function (err, users) { // 如果有匹配返回的是一个[user, user..], 如果
    //没有一个匹配的返回undefined
    console.log('find() ', err, users)
  })
// 查找一个
  UserModel.findOne({_id: '5ae1d0ab28bd750668b3402c'}, function (err, user) { // 如果
    //有匹配返回的是一个 user, 如果没有一个匹配的返回 null
    console.log('findOne() ', err, user)
  })
}
// testFind()

```

U：update

```js
// 3.3. 通过 Model 的 findByIdAndUpdate()更新某个数据
function testUpdate() {
  UserModel.findByIdAndUpdate({_id: '5ae1241cf2dd541a8c59a981'}, {username: 'yyy'},
    function (err, user) {
      console.log('findByIdAndUpdate()', err, user)
    })
}
// testUpdate()

```

D：delete

```js
// 3.4. 通过 Model 的 remove()删除匹配的数据
function testDelete() {
  UserModel.remove({_id: '5ae1241cf2dd541a8c59a981'}, function (err, result) {
    console.log('remove()', err, result)
  })
}
// testDelete()

```

### Demo 用户注册/登录

#### 数据库数据操作模块:db/models.js

包含 n 个能操作 mongodb 数据库集合的 model 的模块

连接数据库

1. 引入 mongoose
2. 连接指定数据库 (URL 只有数据库是变化的)
3. 获取连接对象
4. 绑定连接完成的监听 (用来提示连接成功)

定义出对应特定集合的 Model 并向外暴露

1. 定义 Schema(描述文档结构)
2. 定义 Model(与集合对应, 可以操作集合)
3. 向外暴露 Model

```js
/*1. 连接数据库*/
// 1.1. 引入 mongoose
const mongoose = require('mongoose')
// 1.2. 连接指定数据库(URL 只有数据库是变化的)
mongoose.connect('mongodb://localhost:27017/bossz')
// 1.3. 获取连接对象
const conn = mongoose.connection
// 1.4. 绑定连接完成的监听(用来提示连接成功)
conn.on('connected', function () {
  console.log('数据库连接成功!')
})
/*2. 定义出对应特定集合的 Model 并向外暴露*/
// 2.1. 字义 Schema(描述文档结构)
const userSchema = mongoose.Schema({
  username: {type: String, required: true}, // 用户名
  password: {type: String, required: true}, // 密码
  type: {type: String, required: true}, // 用户类型: dashen/laoban
  header: {type: String}, // 头像名称
  post: {type: String}, // 职位
  info: {type: String}, // 个人或职位简介
  company: {type: String}, // 公司名称
  salary: {type: String} // 工资
})
// 2.2. 定义 Model(与集合对应, 可以操作集合)，集合自动命名为users
const UserModel = mongoose.model('user', userSchema)
// 2.3. 向外暴露 Model
exports.UserModel = UserModel

```

#### 路由器模块: routes/index.js

```js
// 引入 md5 加密函数库
const md5 = require('blueimp-md5')
// 引入 UserModel
const UserModel = require('../db/models').UserModel
const filter = {password: 0} // 查询时过滤出指定的属性

```

**注册路由**

```js
// 注册路由
//post方式还是get方式，接受两个参数路径；回调函数
router.post('register',(req,res)=>{
// 回调函数内部：获取请求参数、处理、返回响应数据
  // 参数的名字依照文档
  const {username,password,type} = req.body
  //判断用户是否已经存在，根据(username)查找
  UserModel.findOne({username},(err,user)=>{
    //如果user有值，说明用户名已存在
    if(user){
      //返回错误信息:都是JSON对象
      res.send({code:1,msg:"用户名已存在"})
    }else{
      //保存用户信息
      // 先对密码进行加密
      new UserModel({username,type,password:md5(password)}).save((err,user)=>{
        //返回包含user的JSON数据,但是不能返回密码
        const data = {username,type,_id:user._id}
        //用户注册成功，帮他登陆，并且标识用户已经登陆
        //方法一：cookie
        //生成一个 cookie(userid: user._id), 并交给浏览器保存
        res.cookie('userid', user._id, {maxAge: 1000*60*60*24*7})
        //前者是用户的user._id，后两者指定持久化 cookie, 浏览器会保存在本地文件
        //方法二：session，参考vue项目
        res.send({code:1,data:data})
      })
    }
  })
})

```

**登陆路由**

```js
//登陆路由
router.post('/login',(req,res)=>{
  const{username,password}= req.body
  //根据username和password查询数据库users
  UserModel.findOne({username,password:md5(password)},{password:0},function (err, user) {
    if(user){
      //user存在，登陆成功
      res.cookie('userid', user._id, {maxAge: 1000*60*60*24*7})
      res.send({code:0,data:user})
    } else {
      //user不存在，登陆失败
      res.send({code:1,msg:"用户名或密码不正确"})
    }
  })
})

```

#### 注意

- 向外暴露的是一个 export 对象，所以必须.UserModel 才能获取到真正的 UserModel
  - `const {UserModel} = require('../db/models').UserModel`
- 设置了必须，但是参数操作的时候却忽略了，会爆出奇怪的错误，导致无法第一时间定位到，因为形式参数的误传
  - `type: {type: *String*, required: true}`
- 使用 postman 测试接口：mongoose 内部的语法不可以使用 es6 的箭头函数，会导致参数未定义

# HTTP 响应控制

## 配置跨域

## 控制缓存

# Cookie 和 Session

+ http://wiki.jikexueyuan.com/project/node-lessons/cookie-session.html

## NodeJS 中

- `response.setHeader(name, value)`
- Sets a single header value for implicit headers. If this header already exists in the to-be-sent headers, its value will be replaced. Use an array of strings here to send multiple headers with the same name.

  ```js
  response.setHeader('Content-Type', 'text/html');
  response.setHeader('Set-Cookie', ['type=ninja', 'language=javascript']);
  ```

- Attempting to set a header field name or value that contains invalid characters will result in a [`TypeError`](https://nodejs.org/dist/latest-v8.x/docs/api/errors.html#errors_class_typeerror) being thrown.
- When headers have been set with [`response.setHeader()`](https://nodejs.org/dist/latest-v8.x/docs/api/http.html#http_response_setheader_name_value), they will be merged with any headers passed to [`response.writeHead()`](https://nodejs.org/dist/latest-v8.x/docs/api/http.html#http_response_writehead_statuscode_statusmessage_headers), with the headers passed to[`response.writeHead()`](https://nodejs.org/dist/latest-v8.x/docs/api/http.html#http_response_writehead_statuscode_statusmessage_headers) given precedence.

  ```js
  // returns content-type = text/plain
  const server = http.createServer((req, res) => {
    res.setHeader('Content-Type', 'text/html');
    res.setHeader('X-Foo', 'bar');
    res.writeHead(200, { 'Content-Type': 'text/plain' });
    res.end('ok');
  });
  ```

- 现在，对该服务器发起的每一次**新请求**，浏览器都会将之前保存的 Cookie 信息通过 [`Cookie`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Cookie) 请求头部再发送给服务器。

## Express

- express 在 4.x 版本之后，session 管理和 cookies 等许多模块都不再直接包含在 express 中，而是需要单独添加相应模块。
- express4 中操作 cookie 使用 `cookie-parser` 模块 (<https://github.com/expressjs/cookie-parser> )。

  ```js
  var express = require('express');
  // 首先引入 cookie-parser 这个模块
  var cookieParser = require('cookie-parser');
  
  var app = express();
  app.listen(3000);
  
  // 使用 cookieParser 中间件，cookieParser(secret, options)
  // 其中 secret 用来加密 cookie 字符串（下面会提到 signedCookies）
  // options 传入上面介绍的 cookie 可选参数
  app.use(cookieParser());
  
  app.get('/', function (req, res) {
    // 如果请求中的 cookie 存在 isVisit, 则输出 cookie
    // 否则，设置 cookie 字段 isVisit, 并设置过期时间为1分钟
    if (req.cookies.isVisit) {
      console.log(req.cookies);
      res.send("再次欢迎访问");
    } else {
      res.cookie('isVisit', 1, {maxAge: 60 * 1000});
      res.send("欢迎第一次访问");
    }
  });
  ```

# Express 中的 Session

- express 中操作 session 要用到 `express-session` (<https://github.com/expressjs/session> ) 这个模块，主要的方法就是 `session(options)`，其中 options 中包含可选参数，主要有：
  - name: 设置 cookie 中，保存 session 的字段名称，默认为 `connect.sid` 。
  - store: session 的存储方式，默认存放在内存中，也可以使用 redis，mongodb 等。express 生态中都有相应模块的支持。
  - secret: 通过设置的 secret 字符串，来计算 hash 值并放在 cookie 中，使产生的 signedCookie 防篡改。
  - cookie: 设置存放 session id 的 cookie 的相关选项，默认为
    - (default: { path: '/', httpOnly: true, secure: false, maxAge: null })
  - genid: 产生一个新的 session_id 时，所使用的函数， 默认使用 `uid2` 这个 npm 包。
  - rolling: 每个请求都重新设置一个 cookie，默认为 false。
  - resave: 即使 session 没有被修改，也保存 session 值，默认为 true。

## 在内存中存储 Session

- `express-session` 默认使用内存来存 session，对于开发调试来说很方便。

  ```js
  var express = require('express');
  // 首先引入 express-session 这个模块
  var session = require('express-session');
  
  var app = express();
  app.listen(5000);
  
  // 按照上面的解释，设置 session 的可选参数
  app.use(session({
    secret: 'recommand 128 bytes random string', // 建议使用 128 个字符的随机字符串
    cookie: { maxAge: 60 * 1000 }
  }));
  
  app.get('/', function (req, res) {
  
    // 检查 session 中的 isVisit 字段
    // 如果存在则增加一次，否则为 session 设置 isVisit 字段，并初始化为 1。
    if(req.session.isVisit) {
      req.session.isVisit++;
      res.send('<p>第 ' + req.session.isVisit + '次来此页面</p>');
    } else {
      req.session.isVisit = 1;
      res.send("欢迎第一次来这里");
      console.log(req.session);
    }
  });
  ```

## 在 Redis 中存储 Session

- http://wiki.jikexueyuan.com/project/node-lessons/cookie-session.html
