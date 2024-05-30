---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2020-10-07-Wed, 1:27:00 pm","date-modified":"2023-04-29-Sat, 8:19:15 pm","permalink":"/programming/back-end/sequelize/","dgPassFrontmatter":true}
---


Sequelize 的使用

官方文档：https://sequelize.org/v5/manual/querying.html

中文文档：https://github.com/demopark/sequelize-docs-Zh-CN 就是一坨

# 理解

## ORM

ORM 是「对象关系映射」的翻译，英语全称为 Object Relational Mapping，它是一种程序设计技术，用于实现面向对象编程语言里不同类型系统的数据之间的转换。从效果上说，它其实是创建了一个可在编程语言里使用的「虚拟对象数据库」。

随着面向对象软件开发方法的发展，ORM 的概念应运而生，它用来把对象模型表示的对象，映射到基于 SQL 的关系模型数据库结构中去。这样，我们在具体的操作实体数据库的时候，就不需要再去和复杂的 SQL 语句打交道，只需简单的操作实体对象的属性和方法，就可以达到操作数据库的效果。

ORM 技术是在对象和数据库之间提供了一条桥梁，前台的对象型数据和数据库中的关系型的数据通过这个桥梁来相互转化。

# 初始化

## 安装 Mysql

到官网 <https://www.mysql.com/downloads> 下载对应版本，并安装数据库。

安装教程可参考：<https://blog.csdn.net/qq_37350706/article/details/81707862>

## 安装 Sequelize

Sequelize 类是引用 sequlize 模块后获取一个顶级对象，我们通过它来创建 sequlize 实例，也可以通过该对象来获取模内其它对象的引用，如：Utils 工具类、Transaction 事务类等。创建实例后，可以通过实例来创建或定义 Model（模型）、执行查询、同步数据库结构等操作。

```js
npm install sequelize --save
```

安装 mysql ， mysql2 模块

```js
npm install sequelize mysql mysql2 --save
```

# 链接数据库

[getting-started](https://github.com/demopark/sequelize-docs-Zh-CN/blob/master/core-concepts/getting-started.md)

## 实例化数据库

在根目录创建 config/db.js 文件：

`require` 引用后，会指向 `Sequelize` 的主类的构造函数，引用后就可以通过 `new` 关键字进行实例化

实例化后就会以连接池的形式连接到所使用的数据库。

语法结构如下：

```js
const Sequelize = require('sequelize');

// 方法 1: 传递一个连接 URI
const sequelize = new Sequelize('sqlite::memory:') // Sqlite 示例
const sequelize = new Sequelize('postgres://user:pass@example.com:5432/dbname') // Postgres 示例
// 方法 2: 分别传递参数 (其它数据库)
const sequelize = new Sequelize('database', 'username', 'password', {
  host: 'localhost',
  dialect: /* 选择 'mysql' | 'mariadb' | 'postgres' | 'mssql' 其一 */
});
```

> 注意： 所连接的数据库必须存在，若不存在先创建数据库，其中 dbName 请替换成相应的数据库名称
>
> 创建数据库命令： CREATE DATABASE **dbName** DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;）

示例：

```js
const Sequelize = require('sequelize');

// 前三个参数分别是 数据库的名称 数据库账号 数据库密码，最后一个参数为相应的参数配置
const demo = new Sequelize('demo', 'root', '123456', {
    host: 'localhost',
    dialect: 'mysql',
    dialectOptions: {
        // 字符集
        charset: "utf8mb4",
        collate: "utf8mb4_unicode_ci",
        supportBigNumbers: true,
        bigNumberStrings: true
    },
    pool: {
        max: 5,
        min: 0,
    },
    timezone: '+08:00', //东八时区
    define:{
      timestamps: false // 取消 Sequelzie 自动给数据表加入时间戳（createdAt 以及 updatedAt）
    }
});

module.exports = {
    demo
}
```

## Options 参数对象

**[options.host='localhost']**

+ String
+ 连接数据库的主机

**[options.dialect='mysql']**

+ String
+ 要连接的数据库类型。可选值有：mysql、postgres、sqlite、mariadb、mssql

**[options.dialectOptions]**

+ Object
+ ```js
  dialectOptions: {
    // 字符集
    charset: "utf8mb4",
    collate: "utf8mb4_unicode_ci",
    supportBigNumbers: true,
    bigNumberStrings: true
  }
  ```

**[options.pool={}]**

+ 使用连接池连接，默认为 `true`
+ [options.pool.maxConnections]
+ [options.pool.minConnections]
+ [options.pool.maxIdleTime]：连接最大空置时间（毫秒），超时后将释放连接

  ```js
  // 已弃用
  acquire: 30000,
  idle: 10000
  ```

**[options.timezone='+00:00']**

+ String
+ 时间转换时从数据库得到的 JavaScript 时间。这个时区将应用于连接服务器的 NOW、CURRENT_TIMESTAMP 或其它日期函数

  ```js
  timezone: '+08:00', //东八时区
  ```

**[options.define={}]**

+ Object
+ 定义模型的选项，默认为 ['sequelize.define'选项](https://itbilu.com/nodejs/npm/VkYIaRPz-.html#api-instance-define)

  ```js
  define:{
    timestamps: false // 取消 Sequelzie 自动给数据表加入时间戳（createdAt 以及 updatedAt）
  }
  ```

## 新旧数据库

如果你是从头开始一个项目,且你的数据库尚不存在,那么一开始就可以使用 Sequelize,以便自动创建数据库中的每个表.

除此之外,如果你想使用 Sequelize 连接到已经充满了表和数据的数据库,那也可以正常工作！ 在两种情况下,Sequelize 都能满足你的要求.

## 记录日志

默认情况下,Sequelize 将记录控制台执行的每个 SQL 查询. 可以使用 `options.logging` 参数来自定义每次 Sequelize 记录某些内容时将执行的函数. 默认值为 `console.log`,使用该值时仅显示日志函数调用的第一个参数. 例如,对于查询日志记录,第一个参数是原始查询,第二个参数 (默认情况下是隐藏的) 是 Sequelize 对象.

`options.logging` 的常用值：

```js
const sequelize = new Sequelize('sqlite::memory:', {
  // 选择一种日志记录参数
  logging: console.log,                  // 默认值,显示日志函数调用的第一个参数
  logging: (...msg) => console.log(msg), // 显示所有日志函数调用参数
  logging: false,                        // 禁用日志记录
  logging: msg => logger.debug(msg),     // 使用自定义记录器(例如Winston 或 Bunyan),显示第一个参数
  logging: logger.debug.bind(logger)     // 使用自定义记录器的另一种方法,显示所有消息
});
```

# 模型

## 概念

模型是 Sequelize 的本质. 模型是代表数据库中表的抽象. 在 Sequelize 中,它是一个 [Model](https://sequelize.org/master/class/lib/model.js~Model.html) 的扩展类.

该模型告诉 Sequelize 有关它代表的实体的几件事,例如数据库中表的名称以及它具有的列 (及其数据类型).

Sequelize 中的模型有一个名称. 此名称不必与它在数据库中表示的表的名称相同. 通常,模型具有单数名称 (例如,`User`),而表具有复数名称 (例如, `Users`),当然这是完全可配置的.

模型的实例时什么？

# 模型定义

在 Sequelize 中可以用两种等效的方式定义模型：

- 调用 [`sequelize.define(modelName, attributes, options)`](https://sequelize.org/master/class/lib/sequelize.js~Sequelize.html#instance-method-define)
- 扩展 [Model](https://sequelize.org/master/class/lib/model.js~Model.html) 并调用 [`init(attributes, options)`](https://sequelize.org/master/class/lib/model.js~Model.html#static-method-init)

定义模型后,可通过其模型名称在 `sequelize.models` 中使用该模型.

为了学习一个示例,我们将考虑创建一个代表用户的模型,该模型具有一个 `firstName` 和一个 `lastName`. 我们希望将模型称为 `User`,并将其表示的表在数据库中称为 `Users`.

定义该模型的两种方法如下所示. 定义后,我们可以使用 `sequelize.models.User` 访问模型.

## 使用 [`sequelize.define`](https://sequelize.org/master/class/lib/sequelize.js~Sequelize.html#instance-method-define)

```js
const { Sequelize, DataTypes } = require('sequelize');
const sequelize = new Sequelize('sqlite::memory:');

const User = sequelize.define('User', {
  // 在这里定义模型属性
  firstName: {
    type: DataTypes.STRING,
    allowNull: false
  },
  lastName: {
    type: DataTypes.STRING
    // allowNull 默认为 true
  }
}, {
  // 这是其他模型参数
});

// `sequelize.define` 会返回模型
console.log(User === sequelize.models.User); // true
```

在内部,`sequelize.define` 调用 `Model.init`,因此两种方法本质上是等效的.

## 列参数

参考：https://github.com/demopark/sequelize-docs-Zh-CN/blob/master/core-concepts/model-basics.md#uuid

**defaultValue**

+ 设置属性的默认值

  ```js
  flag: { 
    type: Sequelize.BOOLEAN, 
    defaultValue: true
  },
  ```

**allowNull**

+ ```js
  // 将allowNull设置为false会将NOT NULL添加到列中，
  // 这意味着当列为空时执行查询时将从DB抛出错误。 
  // 如果要在查询DB之前检查值不为空，请查看下面的验证部分。
  title: { type: Sequelize.STRING, allowNull: false},
  ```

**primaryKey**

+ ```js
  identifier: { 
  	type: Sequelize.STRING, 
  	primaryKey: true
  },
  ```

**autoIncrement**

+ autoIncrement 选项用于创建一个自增的整型列

  ```js
  incrementMe: { 
    type: Sequelize.INTEGER, 
    autoIncrement: true 
  },
  ```

**references**

+ 通过 references 选项可以创建外键

  ```js
   bar_id: {
     type: Sequelize.INTEGER,
     references: {
       // 引用另一个模型
       model: Bar,
       // 连接模型的列表
       key: 'id',
     }
   }
  ```

## Options

+ 定义模型时，可以通过配置来设置列名等相关信息：

  ```js
  var Bar = sequelize.define('bar', { /* bla */ }, {
    // 不要添加时间戳属性 (updatedAt, createdAt)
    timestamps: false,
  
    // 不从数据库中删除数据，而只是增加一个 deletedAt 标识当前时间
    // paranoid 属性只在启用 timestamps 时适用
    paranoid: true,
  
    // 不使用驼峰式命令规则，这样会在使用下划线分隔
    // 这样 updatedAt 的字段名会是 updated_at
    underscored: true,
  
    // 禁止修改表名. 默认情况下
    // sequelize会自动使用传入的模型名（define的第一个参数）做为表名
    // 如果你不想使用这种方式你需要进行以下设置
    freezeTableName: true,
  
    // 定义表名
    tableName: 'my_very_custom_table_name'
  })
  ```

## 如何复用逻辑？

我有 4 张表都是类似的，功能也是类似的，只是储存的数据属性不同，有必要创建 4*3 个文件嘛？

表的继承

可以使用模型作为类

# 数据类型

参考：https://github.com/demopark/sequelize-docs-Zh-CN/blob/master/core-concepts/model-basics.md

https://sequelize.org/master/variable/index.html#static-variable-DataTypes

要访问内置数据类型,必须导入 `DataTypes`：

```js
const { DataTypes } = require("sequelize"); // 导入内置数据类型\
```

## 字符串

```js
DataTypes.STRING             // VARCHAR(255)
DataTypes.STRING(1234)       // VARCHAR(1234)
DataTypes.STRING.BINARY      // VARCHAR BINARY
DataTypes.TEXT               // TEXT
DataTypes.TEXT('tiny')       // TINYTEXT
DataTypes.CITEXT             // CITEXT          仅 PostgreSQL 和 SQLite.
```

## 布尔

```js
DataTypes.BOOLEAN            // TINYINT(1)
```

## 数字

```js
DataTypes.INTEGER            // INTEGER
DataTypes.BIGINT             // BIGINT
DataTypes.BIGINT(11)         // BIGINT(11)

DataTypes.FLOAT              // FLOAT
DataTypes.FLOAT(11)          // FLOAT(11)
DataTypes.FLOAT(11, 10)      // FLOAT(11,10)

DataTypes.REAL               // REAL            仅 PostgreSQL.
DataTypes.REAL(11)           // REAL(11)        仅 PostgreSQL.
DataTypes.REAL(11, 12)       // REAL(11,12)     仅 PostgreSQL.

DataTypes.DOUBLE             // DOUBLE
DataTypes.DOUBLE(11)         // DOUBLE(11)
DataTypes.DOUBLE(11, 10)     // DOUBLE(11,10)

DataTypes.DECIMAL            // DECIMAL
DataTypes.DECIMAL(10, 2)     // DECIMAL(10,2)
```

### 无符号和零填充整数 - 仅限于 MySQL/MariaDB

在 MySQL 和 MariaDB 中,可以将数据类型 `INTEGER`, `BIGINT`, `FLOAT` 和 `DOUBLE` 设置为无符号或零填充 (或两者),如下所示：

```js
DataTypes.INTEGER.UNSIGNED
DataTypes.INTEGER.ZEROFILL
DataTypes.INTEGER.UNSIGNED.ZEROFILL
// 你还可以指定大小,即INTEGER(10)而不是简单的INTEGER
// 同样适用于 BIGINT, FLOAT 和 DOUBLE
```

## 日期

```js
DataTypes.DATE       // DATETIME 适用于 mysql / sqlite, 带时区的TIMESTAMP 适用于 postgres
DataTypes.DATE(6)    // DATETIME(6) 适用于 mysql 5.6.4+. 支持6位精度的小数秒
DataTypes.DATEONLY   // 不带时间的 DATE
```

查询的时候，既可以是 Date 类型，也可以是 YYYY-MM-DD 字符串

## UUID

对于 UUID,使用 `DataTypes.UUID`. 对于 PostgreSQL 和 SQLite,它会是 `UUID` 数据类型; 对于 MySQL,它则变成 `CHAR(36)`. Sequelize 可以自动为这些字段生成 UUID,只需使用 `Sequelize.UUIDV1` 或 `Sequelize.UUIDV4` 作为默认值即可：

```js
{
  type: DataTypes.UUID,
  defaultValue: Sequelize.UUIDV4 // 或 Sequelize.UUIDV1
}
```

## 其它

还有其他数据类型,请参见 [其它数据类型](https://github.com/demopark/sequelize-docs-Zh-CN/blob/master/core-concepts/other-topics/other-data-types.md).

## 注意

+ 怎么储存嵌套的对象或者数组？
+ INTEGER(11) 只能放 10 位数
+ `int(M)` 中的 `M` 指示最大显示宽度，最大有效显示宽度是 255，且显示宽度与存储大小或类型包含的值的范围无关。
+ 声明字段是 int 类型的那一刻起，`int` 就是占四个字节，一个字节 8 位，也就是 `4*8=32`，可以表示的数字个数是 2 的 32 次方 `(2^32 = 4 294 967 296个数字)`。这个数字只有 10 位
+ 能储存数组嘛，不能，只能存 JSON 了

# 模型同步

定义模型时,你要告诉 Sequelize 有关数据库中表的一些信息. 但是,如果该表实际上不存在于数据库中怎么办？ 如果存在,但具有不同的列,较少的列或任何其他差异,该怎么办？

这就是模型同步的来源.可以通过调用一个异步函数 (返回一个 Promise)[`model.sync(options)`](https://sequelize.org/master/class/lib/model.js~Model.html#static-method-sync). 通过此调用,Sequelize 将自动对数据库执行 SQL 查询. 请注意,这仅更改数据库中的表,而不更改 JavaScript 端的模型.

- `User.sync()` - 如果表不存在,则创建该表 (如果已经存在,则不执行任何操作)
- `User.sync({ force: true })` - 将创建表,如果表已经存在,则将其首先删除
- `User.sync({ alter: true })` - 这将检查数据库中表的当前状态 (它具有哪些列,它们的数据类型等),然后在表中进行必要的更改以使其与模型匹配.

示例:

```js
await User.sync({ force: true });
console.log("用户模型表刚刚(重新)创建！");
```

# 模型使用

创建了模型之后可以直接使用增删改查方法

# 规范

虽然我至今为止都无法理解整个 modules 的意义，但是他确实好用，我也不知道是在哪里看到的了

+ 命名规范？还是按照 JS 来的吧
+ 模型应以单词的单数形式定义. 例：

  ```
  sequelize.define('foo', { name: DataTypes.STRING });
  ```

  上面的模型名称是 `foo`(单数),表名称是 `foos`,因为 Sequelize 会自动获取表名称的复数形式.

+ 文件名用复数，变量用单数

## 关联

### V5 外键

+ ```js
  // It is possible to create foreign keys:
  foreignKey: {
    type: DataTypes.STRING(45),
      references: {
        model: 'interviewer',
          key: 'pk',
      }
   }
  ```

### 概述

+ Sequelize 支持标准关联关系: [一对一](https://en.wikipedia.org/wiki/One-to-one_(data_model)), [一对多](https://en.wikipedia.org/wiki/One-to-many_(data_model)) 和 [多对多](https://en.wikipedia.org/wiki/Many-to-many_(data_model)).
+ 为此,Sequelize 提供了 **四种** 关联类型,并将它们组合起来以创建关联：

  - `HasOne` 关联类型
  - `BelongsTo` 关联类型
  - `HasMany` 关联类型
  - `BelongsToMany` 关联类型

+ 四种关联类型的定义非常相似. 假设我们有两个模型 `A` 和 `B`. 告诉 Sequelize 两者之间的关联仅需要调用一个函数：

  ```js
  const A = sequelize.define('A', /* ... */);
  const B = sequelize.define('B', /* ... */);
  
  A.hasOne(B); // A 有一个 B
  A.belongsTo(B); // A 属于 B
  A.hasMany(B); // A 有多个 B
  A.belongsToMany(B, { through: 'C' }); // A 属于多个 B , 通过联结表 C
  ```

+ 它们都接受一个对象作为第二个参数 (前三个参数是可选的,而对于包含 `through` 属性的 `belongsToMany` 是必需的)： They all accept an options object as a second parameter

  ```
  A.hasOne(B, { /* 参数 */ });
  A.belongsTo(B, { /* 参数 */ });
  A.hasMany(B, { /* 参数 */ });
  A.belongsToMany(B, { through: 'C', /* 参数 */ });
  ```

+ 关联的定义顺序是有关系的. 换句话说,对于这四种情况,定义顺序很重要. 在上述所有示例中,`A` 称为 **源** 模型,而 `B` 称为 **目标** 模型. 此术语很重要.
+ `A.hasOne(B)` 关联意味着 `A` 和 `B` 之间存在一对一的关系,外键在目标模型 (`B`) 中定义.
+ `A.belongsTo(B)` 关联意味着 `A` 和 `B` 之间存在一对一的关系,外键在源模型中定义 (`A`).
+ `A.hasMany(B)` 关联意味着 `A` 和 `B` 之间存在一对多关系,外键在目标模型 (`B`) 中定义.
+ 这三个调用将导致 Sequelize **自动将外键添加到适当的模型中**(除非它们已经存在).
+ `A.belongsToMany(B, { through: 'C' })` 关联意味着将表 `C` 用作 [联结表](https://en.wikipedia.org/wiki/Associative_entity),在 `A` 和 `B` 之间存在多对多关系. 具有外键 (例如,`aId` 和 `bId`). Sequelize 将自动创建此模型 `C`(除非已经存在),并在其上定义适当的外键.

  > 注意：在上面的 `belongsToMany` 示例中,字符串 (`'C'`) 被传递给 `through` 参数. 在这种情况下,Sequelize 会自动使用该名称生成模型. 但是,如果已经定义了模型,也可以直接传递模型.

+ 这些是每种关联类型中涉及的主要思想. 但是,这些关系通常成对使用,以便 Sequelize 更好地使用. 这将在后文中看到.

### 一对一关系

- 创建一个 **一对一** 关系, `hasOne` 和 `belongsTo` 关联一起使用;

#### 哲理

+ 假设我们有两个模型,`Foo` 和 `Bar`.我们要在 Foo 和 Bar 之间建立一对一的关系.我们知道在关系数据库中,这将通过在其中一个表中建立外键来完成.因此,在这种情况下,一个非常关键的问题是：我们希望该外键在哪个表中？换句话说,我们是要 `Foo` 拥有 `barId` 列,还是 `Bar` 应当拥有 `fooId` 列？
+ 原则上,这两个选择都是在 Foo 和 Bar 之间建立一对一关系的有效方法.但是,当我们说 *“Foo 和 Bar 之间存在一对一关系”* 时,尚不清楚该关系是 *强制性* 的还是可选的.换句话说,Foo 是否可以没有 Bar 而存在？ Foo 的 Bar 可以存在吗？这些问题的答案有助于帮我们弄清楚外键列在哪里.

#### 示例

+ 我们假设我们有两个模型,即 `Foo` 和 `Bar`. 我们想要在它们之间建立一对一的关系,以便 `Bar` 获得 `fooId` 列.
+ 实现该目标的主要设置如下：

  ```js
  Foo.hasOne(Bar);
  Bar.belongsTo(Foo);
  ```

+ 由于未传递任何参数,因此 Sequelize 将从模型名称中推断出要做什么. 在这种情况下,Sequelize 知道必须将 `fooId` 列添加到 `Bar` 中.
+ 这样,在上述代码之后调用 `Bar.sync()` 将产生以下 SQL(例如,在 PostgreSQL 上)：

  ```sql
  CREATE TABLE IF NOT EXISTS "foos" (
    /* ... */
  );
  CREATE TABLE IF NOT EXISTS "bars" (
    /* ... */
    "fooId" INTEGER REFERENCES "foos" ("id") ON DELETE SET NULL ON UPDATE CASCADE
    /* ... */
  );
  ```

#### 参数

+ 可以将各种参数作为关联调用的第二个参数传递.

`onDelete` 和 `onUpdate`

+ 例如,要配置 `ON DELETE` 和 `ON UPDATE` 行为,你可以执行以下操作：

  ```
  Foo.hasOne(Bar, {
    onDelete: 'RESTRICT',
    onUpdate: 'RESTRICT'
  });
  Bar.belongsTo(Foo);
  ```

  可用的参数为 `RESTRICT`, `CASCADE`, `NO ACTION`, `SET DEFAULT` 和 `SET NULL`.

  一对一关联的默认值, `ON DELETE` 为 `SET NULL` 而 `ON UPDATE` 为 `CASCADE`.

**自定义外键**

+ 上面显示的 `hasOne` 和 `belongsTo` 调用都会推断出要创建的外键应称为 `fooId`. 如要使用其他名称,例如 `myFooId`：

  ```js
  // 方法 1
  Foo.hasOne(Bar, {
    foreignKey: 'myFooId'
  });
  Bar.belongsTo(Foo);
  
  // 方法 2
  Foo.hasOne(Bar, {
    foreignKey: {
      name: 'myFooId'
    }
  });
  Bar.belongsTo(Foo);
  
  // 方法 3
  Foo.hasOne(Bar);
  Bar.belongsTo(Foo, {
    foreignKey: 'myFooId'
  });
  
  // 方法 4
  Foo.hasOne(Bar);
  Bar.belongsTo(Foo, {
    foreignKey: {
      name: 'myFooId'
    }
  });
  ```

+ 如上所示,`foreignKey` 参数接受一个字符串或一个对象. 当接收到一个对象时,该对象将用作列的定义,就像在标准的 `sequelize.define` 调用中所做的一样. 因此,指定诸如 `type`, `allowNull`, `defaultValue` 等参数就可以了.

  例如,要使用 `UUID` 作为外键数据类型而不是默认值 (`INTEGER`),只需执行以下操作：

  ```js
  const { DataTypes } = require("Sequelize");
  
  Foo.hasOne(Bar, {
    foreignKey: {
      // name: 'myFooId'
      type: DataTypes.UUID
    }
  });
  Bar.belongsTo(Foo);
  ```

**强制性与可选性关联**

+ 默认情况下,该关联被视为可选. 换句话说,在我们的示例中,`fooId` 允许为空,这意味着一个 Bar 可以不存在 Foo 而存在. 只需在外键选项中指定 `allowNull: false` 即可更改此设置：

  ```
  Foo.hasOne(Bar, {
    foreignKey: {
      allowNull: false
    }
  });
  // "fooId" INTEGER NOT NULL REFERENCES "foos" ("id") ON DELETE RESTRICT ON UPDATE RESTRICT
  ```

### 一对多关系

+ 创建一个 **一对多** 关系, `hasMany` he `belongsTo` 关联一起使用;

#### 哲学

+ 一对多关联将一个源与多个目标连接,而所有这些目标仅与此单个源连接.
+ 这意味着,与我们必须选择放置外键的一对一关联不同,在一对多关联中只有一个选项. 例如,如果一个 Foo 有很多 Bar(因此每个 Bar 都属于一个 Foo),那么唯一明智的方式就是在 `Bar` 表中有一个 `fooId` 列. 而反过来是不可能的,因为一个 `Foo` 会有很多 `Bar`.

#### **示例**

+ 在这个例子中,我们有模型 `Team` 和 `Player`. 我们要告诉 Sequelize,他们之间存在一对多的关系,这意味着一个 Team 有 Player ,而每个 Player 都属于一个 Team.
+ 这样做的主要方法如下：

  ```js
  Team.hasMany(Player);
  Player.belongsTo(Team);
  ```

+ 同样,实现此目标的主要方法是使用一对 Sequelize 关联 (`hasMany` 和 `belongsTo`).
+ 例如,在 PostgreSQL 中,以上设置将在 `sync()` 之后产生以下 SQL：

  ```sql
  CREATE TABLE IF NOT EXISTS "Teams" (
    /* ... */
  );
  CREATE TABLE IF NOT EXISTS "Players" (
    /* ... */
    "TeamId" INTEGER REFERENCES "Teams" ("id") ON DELETE SET NULL ON UPDATE CASCADE,
    /* ... */
  );
  ```

#### **参数**

+ 在这种情况下要应用的参数与一对一情况相同. 例如,要更改外键的名称并确保该关系是强制性的,我们可以执行以下操作：

  ```js
  Team.hasMany(Player, {
    foreignKey: 'clubId'
  });
  Player.belongsTo(Team);
  ```

+ 如同一对一关系, `ON DELETE` 默认为 `SET NULL` 而 `ON UPDATE` 默认为 `CASCADE`.

### 多对多关系

+ https://demopark.github.io/sequelize-docs-Zh-CN/core-concepts/assocs.html

- 创建一个多对多关系, 两个 `belongsToMany` 调用一起使用.

  > 注意: 还有一个 *超级多对多* 关系,一次使用六个关联,将在 [高级多对多关系指南](https://demopark.github.io/sequelize-docs-Zh-CN/core-concepts/advanced-association-concepts/advanced-many-to-many.md) 中进行讨论.

## 创建、使用 Model 对象

+ `sequelize.define()` 方式创建的 Model，直接引入即可使用
+ `sequelize.import()` 方法创建的 Model

  ```js
  const demo = db.demo // 引入数据库
  const UserModel = demo.import('../schemas/user.js') 
  ```

## 使用 Controller

+ 在 Controller 统一控制 Model 进行查询，向外暴露

  ```js
  const getUserInfo = async function (ctx, next){
    const id = ctx.params.id// 获取url里传过来的参数里的id
    const result = await User.getUserById(id);  // 通过await“同步”地返回查询结果
    ctx.response.body = result // 将请求的结果放到response的body里返回
  }
  ```

+ 其实只是 MVC 模式的体现，不属于 Sequelize 定义的部分

# 增删改查（Model 方法）

- 有了 Model 对象，就可以进行增删改查的操作了
- 增删改查的对象都是 **Model 的实例**

## 增

### Model.create()

构建一个新的模型实例，并进行保存。与 `build()` 方法不同的是，此方法除创建新实例外，还会将其保存到对应数据库表中。

```js
create(values, [options]) -> Promise.<Instance>
```

```js
/**
 * @param {object} userInfo
 * @return {object}
 */
const createUser = async function(userInfo){
  let res = await UserModel.create(userInfo)
  return res 
}
```

接受一个对象，对象的属性名与表的列名对应

## 查

Finder 方法旨在从数据库查询数据. 他们 **不** 返回简单的对象,而是返回模型实例. 因为 finder 方法返回模型实例,你可以按照 [*实例*](https://github.com/demopark/sequelize-docs-Zh-CN/blob/v5/instances.md) 的文档中所述,为结果调用任何模型实例成员

通过 get 方法访问实例的值

### Model.findByPk()

通过 primary key 查询查询单个实例（单条数据）。

```js
findById(pk, [options]) -> Promise.<Model>
```

```js
/**
 * @param {number,string,Buffer} pk
 * @return {object}
 */
const getUserByPk = async function(id) {
  const userInfo = await UserModel.findByPk(id)
  return userInfo // 返回数据
}
```

**别名：findByPrimary**

> findById 已经废弃了

### Model.findOne()

查询单个实例（单条数据）。这将会使用 `LIMIT 1` 查询条件，所以回调中总是返回单个实例。

```js
findOne(options]) -> Promise.<Instance>
```

```js
/**
 * @param {string} username
 * @return {object}
 */
const getUserByName = async function(username) {
  const userInfo = await User.findOne({
    // 用await控制异步操作，将返回的Promise对象里的数据返回出来。也就实现了“同步”的写法获取异步IO操作的数据
    where: {
      username:username
    }
  })
  return userInfo // 返回数据
}
```

### Model.findAll()

查询多个实例（多条数据）

```js
findAll([options]) -> Promise.<Array.<Instance>>
```

**别名：all**

### Model.max()

Get the greatest value of a specific attribute within a specific table

查询某个属性的最大值

**返回值**

+ 仅返回该最大值，不会返回整个数据实例

### 异常处理

+ 当查找不到的时候，是什么情况？？？

### 限制,偏移,顺序和分组

查询最后一个：先 order 倒序，然后 limit 1 个即可

```js
// 返回最新的两个tags
museTags = await MuseModel.findAll({
  order: [['date', 'DESC']],
  limit: 2
})
```

顺序的话就不用 order 了，直接限制 1 个即可

## 查询

所有的查询方法都可以通过第二个参数 Option 使用这些规则

### 属性

想要只选择某些属性,可以使用 `attributes` 选项. 通常是传递一个数组:

```js
Model.findAll({
  attributes: ['foo', 'bar']
});
SELECT foo, bar ...
```

属性可以使用嵌套数组来重命名:

```js
Model.findAll({
  attributes: ['foo', ['bar', 'baz']]
});
SELECT foo, bar AS baz ...
```

也可以使用 `sequelize.fn` 来进行聚合:

```js
Model.findAll({
  attributes: [[sequelize.fn('COUNT', sequelize.col('hats')), 'no_hats']]
});
SELECT COUNT(hats) AS no_hats ...
```

使用聚合功能时,必须给它一个别名,以便能够从模型中访问它. 在上面的例子中,你可以使用 `instance.get('no_hats')` 获得帽子数量.

同样,也可以排除某些属性：

```js
Model.findAll({
  attributes: { exclude: ['baz'] }
});

```

```sql
-- Assuming all columns are 'id', 'foo', 'bar', 'baz' and 'qux'
SELECT id, foo, bar, qux FROM ...
```

### Raw

options.raw [boolean](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Boolean) optional Return raw result. See sequelize.query for more information.

直接返回查询结果，不用再次调用 get() 方法去获取值

### Where

https://github.com/demopark/sequelize-docs-Zh-CN/blob/v5/querying.md

### 操作符

#### 组合范围

```js
const result = await MuseModel.findAll({
  where: {
    date: {
      [Op.gte]: from,
      [Op.lte]: to
    }
  }
})
SELECT * FROM `objects` 
WHERE  (date_field BETWEEN '2010-01-30 14:15:55' AND '2010-09-29 10:15:55')
```

## 改

### Model.update()

更新所匹配的多个实例。promise 回调中会返回一个包含一个或两个元素的数组，第一个元素始终表示受影响的行数，第二个元素表示实际影响的行（仅 Postgre`options.returning` 为 true 时受支持）

```js
update(values, options) -> Promise.<Array.<affectedCount, affectedRows>>
```

```js
/**
 * @param {number} id
 * @param {obeject} newInfo
 * @return {object}
 */
const updateUserById = async function(id,newInfo){
  const userInfo = await UserModel.update(newInfo,{
    where:{
      id:id
    }
  })
  return userInfo
}
```

## 删

### Model.destroy()

- 删除多个实例，或设置 deletedAt 的时间戳为当前时间（当启用 `paranoid` 时）

  ```js
  destroy(options) -> Promise.<Integer>
  ```

- 执行成功后返回被删除的行数

  ```js
  /**
   * @param {number} id
   * @return {object}
   */
  const destroyUserById = async function(id){
    const userInfo = await UserModel.destroy({
      where:{
        id:id
      }
    })
    return userInfo
  }
  ```

## 组合

### Model.findOrCreate()

- 查找一行记录，如果不存在则创建实例并保存到数据库中
- 在这个方法中，如果 `options` 对象中没有传入事务，那么会在内部自动创建一个新的事务，以防止在创建完成之前有新匹配查询进入。

  ```js
  findOrCreate(options) -> Promise.<Instance, created>
  ```

# 查询与原始查询

https://itbilu.com/nodejs/npm/VJIR1CjMb.html

## 使用原始查询

- 如，在查询中使用 `AND` 和 `\=`：

  ```
  Model.findAll({
    where: {
      attr1: 42,
      attr2: 'cake'
    }
  })
  // WHERE attr1 = 42 AND attr2 = 'cake'
  ```

- 在查询中使用 `大于`、`小于` 等：

  ```
  Model.findAll({
    where: {
      attr1: {
        $gt: 50
      },
      attr2: {
        $lte: 45
      },
      attr3: {
        $in: [1,2,3]
      },
      attr4: {
        $ne: 5
      }
    }
  })
  // WHERE attr1 > 50 AND attr2 <= 45 AND attr3 IN (1,2,3) AND attr4 != 5
  ```

- 在查询中使用 `OR`：

  ```
  Model.findAll({
    where: {
      name: 'a project',
      $or: [
        {id: [1, 2, 3]},
        {
          $and: [
            {id: {gt: 10}},
            {id: {lt: 100}}
          ]
        }
      ]
    }
  });
  
  //WHERE `Model`.`name` = 'a project' AND (`Model`.`id` IN (1, 2, 3) OR (`Model`.`id` > 10 AND `Model`.`id` < 100));
  ```

- 查询成功后会返回包含多个实例（`instance`）的数组。

# FAQ

## 如何获取表中的最后一个数据？

只能整个表，然后取最后一个
