---
{"dg-publish":true,"permalink":"/programming/back-end/koa/"}
---


session：https://chenshenhai.github.io/koa2-note/note/jsonp/info.html

koa2：<https://koa.bootcss.com/>

sequelize 中文文档：https://itbilu.com/nodejs/npm/V1PExztfb.html

# 搭建基础环境

## Koa

+ Koa 依赖 **node v7.6.0** 或 ES2015 及更高版本和 async 方法支持.
+ 你可以使用自己喜欢的版本管理器快速安装支持的 node 版本：

  ```js
  $ npm i koa
  ```

### 使用 Koa-generator 搭建项目

+ ```js
  npm install koa-generator -g
  ```
+ 使用 koa 命令新建项目

  ```
  koa2  -e project(项目名称)   （-e 代表使用模板引擎ejs）
  ```

+ 安装依赖

  ```
  npm install
  ```

+ 安装完成后，可以直接在项目目录下启动项目

  ```js
  npm start
  ```

### 梳理项目结构

- ```js
  │  app.js
  │  list.txt
  │  package-lock.json
  │  package.json
  │  
  ├─bin
  │      www // 设置端口号
  │      
  ├─config
  │      db.js // 配置链接数据库
  │      
  ├─controllers
  │      user.js // 控制modules
  │      
  ├─modules
  │      user.js // 对表进行CRUD操作
  │         
  ├─routes
  │      index.js // 路由入口
  │      users.js
  │      
  ├─schema
  │      user.js // 定义表
  ├─node_modules // npm包文件夹
  ```
- 其他还有一些 `koa-generator` 展示用的静态文件，可以直接删除

### Live Reload

问题: 每次修改后台应用代码, 需要重新运行命令修改才生效

解决: 使用 nodemon 包

- monitor，监视器

下载: `npm install --save-dev nodemon`

- `package.json` 配置:` "start": "nodemon ./bin/www"`

  ```
  "start": "nodemon ./bin/www --ignore data/",
  ```

  https://github.com/remy/nodemon#config-files

测试: 修改后台任何代码, 会自动重新运行最新的代码 (按下 ctrl + S)

## 使用 Sequelize 操作 Mysql

参考 Sequelize

## 路由

+ 在根目录下创建 router/index.js 这文件可以理解为向外暴露 API 接口，实际上就是路由的访问，然后通过控制器=>模型处理=>返回数据

  ```js
  const router = require('koa-router')()
  const UserController = require('../controllers/users.js')
  
  router.get('/', async (ctx, next) => {
    await ctx.render('index', {
      title: 'Hello Koa 2! test test test'
    })
  })
  
  router.get('/string', async (ctx, next) => {
    ctx.body = 'koa2 string'
  })
  
  router.get('/json', async (ctx, next) => {
    ctx.body = {
      title: 'koa2 json'
    }
  })
  
  
  router.post('/user/:id', UserController.getUserInfo) //用POST请求
  
  module.exports = router
  ```

### 使用路由

+ `koa-generator` 已经帮我们做好了这个工作，可以在 `app.js` 文件处看到，route 已经被挂载到服务器上了

  ```js
  // routes
  app.use(index.routes(), index.allowedMethods())
  app.use(users.routes(), users.allowedMethods())
  ```

## 测试

+ 在 mysql 对应的数据库 表 下 填入一项数据

  ![1555744018264](/img/user/programming/back-end/koa/1555744018264.png)

+ 启动服务器

  ```js
  npm start
  ```

  ![1555744354960](/img/user/programming/back-end/koa/1555744354960.png)

+ 使用 `postman` 工具测试接口

  ![1555744405055](/img/user/programming/back-end/koa/1555744405055.png)

# Koa 基本使用

不知道为什么：

```js
const getInterviewerInfo = async function(ctx,next){
  const pk = ctx.query.id
  const candidateInfo = await Candidate.getInterviewerByPk(pk)
  const result = await User.getUserByPk(candidateInfo.foreignKey)
  ctx.response.body = result
}
```

只能直接返回 result，给 result 添加属性或者复制 result 或者直接返回 JSON 啥的都不行 @@@

## Get 方法

ctx.query.id

## Post 方法

userInfo = ctx.request.body

# HTTP 响应控制

## 常用字段

### response.status

+ 获取响应状态。默认情况下，`response.status` 设置为 `404` 而不是像 node 的 `res.statusCode` 那样默认为 `200`。

## 配置跨域

- 引入 koa2-cors ，安装依赖后，在 app.js 中加入下面代码：

  ```js
  const cors = require('koa2-cors');
  // 不同在哪？
  app.use(cors({
    origin: '*',
    exposeHeaders: ['WWW-Authenticate', 'Server-Authorization'],
    maxAge: 5,
    credentials: true,
    allowMethods: ['GET', 'POST', 'DELETE'],
    allowHeaders: ['Content-Type', 'Authorization', 'Accept'],
  }));
  ```

- ```js
  /**
   * CORS middleware
   *
   * @param {Object} [options]
   *  - {String|Function(ctx)} origin `Access-Control-Allow-Origin`, default is request Origin header
   *  - {String|Array} allowMethods `Access-Control-Allow-Methods`, default is 'GET,HEAD,PUT,POST,DELETE,PATCH'
   *  - {String|Array} exposeHeaders `Access-Control-Expose-Headers`
   *  - {String|Array} allowHeaders `Access-Control-Allow-Headers`
   *  - {String|Number} maxAge `Access-Control-Max-Age` in seconds
   *  - {Boolean} credentials `Access-Control-Allow-Credentials`
   *  - {Boolean} keepHeadersOnError Add set headers to `err.header` if an error is thrown
   * @return {Function} cors middleware
   * @api public
   */
  ```

## 控制缓存

# Koa-session

## 基本使用

```js
const session = require('koa-session');
const Koa = require('koa');
const app = new Koa();
app.keys = ['some secret hurr'];

const CONFIG = {
  key: 'koa:sess', /** (string) cookie key (default is koa:sess) */
  
  /** (number || 'session') maxAge in ms (default is 1 days) */
  /** 'session' will result in a cookie that expires when session/browser is closed */
  /** Warning: If a session cookie is stolen, this cookie will never expire */
  maxAge: 86400000,
  overwrite: true, /** (boolean) can overwrite by js or not (default true) */
  httpOnly: true, /** (boolean) httpOnly or not (default true) */
  signed: true, /** (boolean) signed or not (default true) */
  rolling: false, /** (boolean) Force a session identifier cookie to be set on every response. The expiration is reset to the original maxAge, resetting the expiration countdown. (default is false) */
  renew: false, /** (boolean) renew session when session is nearly expired, so we can always keep user logged in. (default is false)*/
};

app.use(session(CONFIG, app));

app.use(ctx => {
  // ignore favicon
  if (ctx.path === '/favicon.ico') return;

  let n = ctx.session.views || 0;
  ctx.session.views = ++n;
  ctx.body = n + ' views';
});

app.listen(3000);
```

我们看到这个在这个会话状态中，session 中保存了页面访问次数，每次请求的时候，会增加计数再把结果返回给用户。

对于 session 的存储方式，koa-session 同时支持 cookie 和外部存储。

## 使用 Cookie 储存 Session

默认配置下，会使用 cookie 来存储 session 信息，也就是实现了一个 "cookie session"。这种方式对服务端是比较轻松的，不需要额外记录任何 session 信息，但是也有不少限制，比如大小的限制以及安全性上的顾虑。用 cookie 保存时，实现上非常简单，就是对 session(包括过期时间) 序列化后做一个简单的 base64 编码。其结果类似 ：

```js
koa:sess=eyJwYXNzcG9ydCI6eyJ1c2VyIjozMDM0MDg1MTQ4OTcwfSwiX2V4cGlyZSI6MTUxNzI3NDE0MTI5MiwiX21heEFnZSI6ODY0MDAwMDB9;
```

**示例**

```js
  const session = require("koa-session")
  app.use(session({
      signed:false,
      maxAge: 20 * 1000
  },app))
```

## 外部存储

在实际项目中，会话相关信息往往需要再服务端持久化，因此一般都会使用外部存储来记录 session 信息。

外部存储可以是任何的存储系统：

- 可以是内存数据结构，
- 可以是本地的文件
- 也可以是远程的数据库。

**但是这不意味着我们不需要 cookie 了，由于 http 协议的无状态特性，我们依然需要通过 cookie 来获取 session 的标识 (这里叫 externalKey)。**

koa-session 里的 external key 默认是一个时间戳加上一个随机串，因此 cookie 的内容类似：

```js
koa:sess=1517188075739-wnRru1LrIv0UFDODDKo8trbmFubnVmMU;
```

### 在内存中存储 Sessoin

要实现一个外置的存储，用户需要自定义 get(), set() 和 destroy() 函数，分别用于获取、更新和删除 session。一个最简单的实现，我们就采用一个 object 来存储 session，那么可以这么来配置：

```js
let store = {
  storage: {},
  get (key, maxAge) {
    return this.storage[key]
  },
  set (key, sess, maxAge) {
    this.storage[key] = sess
  },
  destroy (key) {
    delete this.storage[key]
  }
}
app.use(session({store}, app))
```

### 在数据库中储存 Session

使用 mongoDB 储存 session

> https://juejin.im/post/5a6ad754f265da3e513352e5#heading-5

```js
const session = require("koa-session")
const MongoStore = require("koa-session-mongo2")
app.use(session({
    store: new MongoStore({
        url:  DB_URL  // your mongodb url  required
        db:'user',
        collection: optional, db session collection name,default "__session"
        signed:false // if true, please set app.keys = ['...']
    }),
    signed:false,
    maxAge: 20 * 1000
},app))
```

使用 mysql 储存 session

> https://chenshenhai.github.io/koa2-note/note/session/info.html

```js
const Koa = require('koa')
const session = require('koa-session-minimal')
const MysqlSession = require('koa-mysql-session')

const app = new Koa()

// 配置存储session信息的mysql
let store = new MysqlSession({
  user: 'root',
  password: 'abc123',
  database: 'koa_demo',
  host: '127.0.0.1',
})

// 存放sessionId的cookie配置
let cookie = {
  maxAge: '', // cookie有效时长
  expires: '',  // cookie失效时间
  path: '', // 写cookie所在的路径
  domain: '', // 写cookie所在的域名
  httpOnly: '', // 是否只用于http请求中获取
  overwrite: '',  // 是否允许重写
  secure: '',
  sameSite: '',
  signed: '',

}

// 使用session中间件
app.use(session({
  key: 'SESSION_ID',
  store: store,
  cookie: cookie
}))

app.use( async ( ctx ) => {

  // 设置session
  if ( ctx.url === '/set' ) {
    ctx.session = {
      user_id: Math.random().toString(36).substr(2),
      count: 0
    }
    ctx.body = ctx.session
  } else if ( ctx.url === '/' ) {

    // 读取session信息
    ctx.session.count = ctx.session.count + 1
    ctx.body = ctx.session
  } 

})

app.listen(3000)
console.log('[demo] session is starting at port 3000')
```

## 分布式 Session

首先是为什么会有这样的概念出现？

先考虑这样一个问题，现在我的应用需要部署在 3 台机器上。是不是出现这样一种情况，我第一次登陆，请求去了机器 1，然后再机器 1 上创建了一个 session；但是我第二次访问时，请求被路由到机器 2 了，但是机器 2 上并没有我的 session 信息，所以得重新登录。当然这种可以通过 nginx 的 IP HASH 负载策略来解决。对于同一个 IP 请求都会去同一个机器。

但是业务发展的越来越大，拆分的越来越多，机器数不断增加；很显然那种方案就不行了。那么这个时候就需要考虑是不是应该将 session 信息放在一个独立的机器上，所以分布式 session 要解决的问题其实就是分布式环境下的 session 共享的问题。

上图中的关于 session 独立部署的方式有很多种，可以是一个独立的数据库服务，也可以是一个缓存服务 (redis，目前比较常用的一种方式，即使用 Redis 来作为 session 缓存服务器)。

# Koa-session 源码

> https://segmentfault.com/a/1190000013039187#articleHeader6

## 主要流程

+ koa-session 的代码结构很简单

  ```js
  index.js          // 定义主流程和扩展context
   \- context.js    // 定义SessionContext类，定义了对session的主要操作
   \- session.js    // 定义session类，只有一些简单的util
   \- util.js       // 对session进行编码解码的util
  ```

### index.js 入口

+ 核心逻辑

  ```js
  module.exports = function(opts, app) {
    // ... 省略部分代码
    
    opts = formatOpts(opts);
    // 给ctx添加一些属性，可用于读写 session
    extendContext(app.context, opts); 
    return async function session(ctx, next) {
      // 获取当前的session，这里设置了一个getter，首次访问时会创建一个新的ContextSession
      const sess = ctx[CONTEXT_SESSION]; 
      // 如果设置了使用外部存储，就从外部存储初始化
      if (sess.store) await sess.initFromExternal(); 
      try {
        await next();
      } catch (err) {
        throw err;
      } finally {
        await sess.commit();
      }
    };
  };
  ```

+ 可以看到 koa-session 的基本流程非常简单

  1. 根据 cookie 或者外部存储初始化 cookie。
  2. 调用 next() 执行后面的业务逻辑，其中可以读取和写入新的 session 内容。
  3. 调用 commit() 把更新后的 session 保存下来。

## Session 初始化

+ 了解了 session 的存储方式，就很容易了解 session 的初始化过程了。

### extendContext(app.context, opts)

+ 在上面的**koa-session 主要流程**中, 可以看到调用了 `extendContext(app.context, opts)`，其作用是给 context 扩充了一些内容，代码如下

  ```js
  /**
   * extend context prototype, add session properties
   *
   * @param  {Object} context koa's context prototype
   * @param  {Object} opts session options
   *
   * @api private
   */
  function extendContext(context, opts) {
    Object.defineProperties(context, {
      [CONTEXT_SESSION]: {
        // getter函数，访问[CONTEXT_SESSION]时触发
        get() {
          if (this[_CONTEXT_SESSION]) return this[_CONTEXT_SESSION];
          // 用于在首次调用时新建一个ContextSession对象，添加到了上下文中
          this[_CONTEXT_SESSION] = new ContextSession(this, opts);
          return this[_CONTEXT_SESSION];
        },
      },
      session: {
        get() {
          // this[CONTEXT_SESSION]触发getter，返回this[_CONTEXT_SESSION]，调用ConextSession实例的get方法
          return this[CONTEXT_SESSION].get();
        },
        set(val) {
          this[CONTEXT_SESSION].set(val);
        },
        configurable: true,
      },
      sessionOptions: {
        get() {
          return this[CONTEXT_SESSION].opts;
        },
      },
    });
  ```

+ `[_CONTEXT_SESSION]` 属性是一个 `ContextSession` 对象，该对象是 sessoin **真正的** holder
+ 这里定义了一个 getter，用于在首次调用时新建一个 `ContextSession` 对象。

  > ContextSession 对象有 get 方法和 set 方法，用于**读写 实际的 sessoin**

+ session 属性就是用于读写 `ContextSession` 对象里的 session 字段

  > 添加到 ctx 上，方便对 session 进行操作，对 ctx.session 的读写 相当于 ContextSession.get()/set()

+ 这里有一点奇怪的是，从 cookie 初始化是在首次调用 `ContextSession.get()` 的时候才进行

  > 向外暴露的结构中可以调用 `ContextSession.get()` 的只有对 `ctx.session` 进行读操作

+ 而从外部存储初始化则是在主流程中就调用了。

### ContextSession 类

+ `ContextSession` 类定义在 koa-session 库的 **context.js 文件**中

#### get() 方法

+ get() 方法代码如下：

  ```js
    /**
     * internal logic of `ctx.session`
     * @return {Session} session object
     *
     * @api public
     */
  
    get() {
      const session = this.session;
      // already retrieved
      if (session) return session;
      // unset
      if (session === false) return null;
  
      // cookie session store
      if (!this.store) this.initFromCookie();
      return this.session;
    }
  ```

#### initFromCookie()

+ `initFromCookie()` 就是从 cookie 的初始化过程，代码很简单，我加了一点注释，最需要注意的就是生成一个 prevHash 来标记当前状态

  ```js
    /**
     * init session from cookie
     * @api private
     */
  
    initFromCookie() {
      debug('init from cookie');
      const ctx = this.ctx;
      const opts = this.opts;
      
  		// 获取cookie，如果不存在就调用create()新建一个空的session
      const cookie = ctx.cookies.get(opts.key, opts);
      if (!cookie) {
        this.create();
        return;
      }
  
      let json;
      debug('parse %s', cookie);
      try {
        // 解析base64编码的cookie内容
        json = opts.decode(cookie);
      } catch (err) {
  			// 省略错误处理内容
      }
  
      debug('parsed %j', json);
  		// 对于session检查有效性，如果失败（比如已经过期）就新建一个session
      if (!this.valid(json)) {
        this.create();
        return;
      }
  
      // support access `ctx.session` before session middleware
      // 根据cookie的内容来创建session
      this.create(json);
      // *** 记录当前session的hash值，用于在业务流程完成判断是否有更新 ***
      this.prevHash = util.hash(this.session.toJSON());
    }
  ```

#### initFromExternal()

+ initFromExternal() 就是从外部存储初始化 session，和 cookie 初始化类似

  ```js
    async initFromExternal() {
      debug('init from external');
      const ctx = this.ctx;
      const opts = this.opts;
  
      // FK: 对于外部存储，cookie中的内容就是external key
      const externalKey = ctx.cookies.get(opts.key, opts);
      debug('get external key from cookie %s', externalKey);
      
      // FK: 如果external key不存在，就新建一个
      if (!externalKey) {
        // create a new `externalKey`
        this.create();
        return;
      }
  
      // FK: 如果在外部存储中找不到相应的session，就新建一个
      const json = await this.store.get(externalKey, opts.maxAge, { rolling: opts.rolling });
      if (!this.valid(json, externalKey)) {
        // create a new `externalKey`
        this.create();
        return;
      }
  
      // create with original `externalKey`
      // FK: 根据外部存储的内容来创建session
      this.create(json, externalKey);
      // FK: *** 记录当前session的hash值，用于在业务流程完成判断是否有更新 ***
      this.prevHash = util.hash(this.session.toJSON());
    }
  ```

#### set() 方法

+ set() 方法代码如下：

  ```js
    /**
     * internal logic of `ctx.session=`
     * @param {Object} val session object
     *
     * @api public
     */
  
    set(val) {
      if (val === null) {
        this.session = false;
        return;
      }
      if (typeof val === 'object') {
        // use the original `externalKey` if exists to avoid waste storage
        this.create(val, this.externalKey);
        return;
      }
      throw new Error('this.session can only be set as null or an object.');
    }
  ```

## Session 提交

### commit()

- 在**主流程**我们已经看到，在业务逻辑处理之后，会调用 `sess.commit()` 来提交修改后的 session。根据 session 的存储方式，提交的 session 会保存到 cookie 中或者是外部存储中。
- ```js
  async commit() {
    // 在commit中第一次访问了ctx.session，触发了getter，如果...，则初始化session
    const session = this.session;
    const opts = this.opts;
    const ctx = this.ctx;
    
    // not accessed
    if (undefined === session) return;
    
    // removed，如果session被显式定义为false，则无法初始化session，此时就会进行删除
    if (session === false) {
      await this.remove();
      return;
    }
    
    const reason = this._shouldSaveSession();
    debug('should save session: %s', reason);
    if (!reason) return;
    		// 提供了hook给我们使用
    if (typeof opts.beforeSave === 'function') {
      debug('before save');
      opts.beforeSave(ctx, session);
    }
    const changed = reason === 'changed';
    await this.save(changed);
  }
  ```

### _shouldSaveSession()

- commit() 的过程就是判断是否要保存/删除 cookie，删除的条件比较简单，保存 cookie 的条件又调用了 _shouldSaveSession()，代码如下

  ```js
  _shouldSaveSession() {
      // 省略部分代码。。。
  
      // save if session changed
    	// 在读写的时候记录的prevHash
      const changed = prevHash !== util.hash(json);
      if (changed) return 'changed';
  
      // save if opts.rolling set
      if (this.opts.rolling) return 'rolling';
  
      // save if opts.renew and session will expired
      if (this.opts.renew) {
        const expire = session._expire;
        const maxAge = session.maxAge;
        // renew when session will expired in maxAge / 2
        if (expire && maxAge && expire - Date.now() < maxAge / 2) return 'renew';
      }
  
      return '';
    }
  ```

- 可见保存 session 的情况包括

  1. 如果 session 有变动
  2. 在 config 里设置了 rolling 为 true，也就是每次都更新 session
  3. 在 config 里设置了 renew 为 true，且有效期已经过了一半，需要更新 session

### save()

- 一旦满足任何一个条件，就会调用 save() 操作来保存 cookie

  ```js
    async save(changed) {
      // 省略部分代码。。。
      // save to external store
      if (externalKey) {
        debug('save %j to external key %s', json, externalKey);
        if (typeof maxAge === 'number') {
          // ensure store expired after cookie
          maxAge += 10000;
        }
        await this.store.set(externalKey, json, maxAge, {
          changed,
          rolling: opts.rolling,
        });
        this.ctx.cookies.set(key, externalKey, opts);
        return;
      }
  
      // save to cookie
      debug('save %j to cookie', json);
      json = opts.encode(json);
      debug('save %s', json);
  
      this.ctx.cookies.set(key, json, opts);
    }
  ```

- 和初始化类似，save() 操作也是分为 cookie 存储和外部存储两种方式分别操作。
- 至此，对于 session 的基本操作流程应该都已经清楚了。

## 流程图

```flow
index.js=>start: index.js
extendContext=>operation: extendContext(app.context, opts) 
[CONTEXT_SESSION]=>subroutine: 添加[CONTEXT_SESSION]访问器属性
session=>subroutine: 添加sessoin访问器属性
sess=>operation: sess = ctx[CONTEXT_SESSION]
getter=>subroutine: 触发[[CONTEXT_SESSION]]getter函数
sess_is_exist=>condition: this[_CONTEXT_SESSION] 存在？
return=>subroutine: 返回ctx[_CONTEXT_SESSION]
create=>subroutine: 新建一个ContextSession对象，赋值给ctx[_CONTEXT_SESSION]
initFromCookie=>subroutine: ContextSession类的getter函数用于从Cookie初始化sessoin
cond=>condition: sess.store === true
initFromExternal=>operation: sess.initFromExternal()
next=>operation: next()
someOp=>subroutine: 执行其他操作，可能包含对sessoin的读写
commit=>operation: commit()，用于提交sessoin
session_getter=>subroutine: 访问ctx.sessoin
如果不是外部储存，会触发initFromCookie()
should_save=>condition: 是否应该保存session
yes_save=>subroutine: changed rolling renew 均会更新session
save=>subroutine: 更新后，存入cookie/外部储存中
no_save=>subroutine: sesion没有更新
不用保存
end=>end


index.js->extendContext
extendContext->[CONTEXT_SESSION]->session->sess
sess(bottom)->getter->sess_is_exist
sess_is_exist(no)->create->initFromCookie->cond
sess_is_exist(yes)->return->cond
cond(yes)->initFromExternal(bottom)->next
cond(no)->next
next->commit->session_getter->should_save
should_save(yes)->yes_save->save->end
should_save(no)->no_save->end
```
