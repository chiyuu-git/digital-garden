---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2022-03-22-Tue, 7:02:57 pm","date-modified":"2023-04-29-Sat, 8:19:54 pm","permalink":"/programming/back-end/typeorm-basic/","dgPassFrontmatter":true}
---


中文文档一：https://typeorm.bootcss.com/

中文文档二：https://typeorm.biunav.com/zh/

中文文档其三：https://orkhan.gitbook.io/typeorm/docs/zh_cn/logging

# 数据库

## 什么是 `Connection`[#](https://typeorm.bootcss.com/connection#什么是connection)

只有在建立连接后才能与数据库进行交互。 TypeORM 的 `Connection` 不会像看起来那样设置单个数据库连接，而是设置连接池。 如果你对数据库连接感兴趣，请参阅 `QueryRunner` 文档。 `QueryRunner` 的每个实例都是一个独立的数据库连接。一旦调用 `Connection` 的 `connect` 方法，就建立连接池设置。 如果使用 `createConnection` 函数设置连接，则会自动调用 `connect` 方法。调用 `close` 时会断开连接（关闭池中的所有连接）。 通常情况下，你只能在应用程序启动时创建一次连接，并在完全使用数据库后关闭它。实际上，如果要为站点构建后端，并且后端服务器始终保持运行,则不需要关闭连接。

## 连接选项 Connection Options

### 概述

连接选项是你传递给 `createConnection` 或在 `ormconfig` 文件中定义的连接配置。不同的数据库有自己的特定连接选项。

### 常用的连接选项 [#](https://typeorm.bootcss.com/connection-options#常用的连接选项)

- `type` - 数据库类型。你必须指定要使用的数据库引擎。该值可以是 "mysql"，"postgres"，"mariadb"，"sqlite", "better-sqlite3"，"cordova"，"nativescript"，"oracle"，"mssql"，"mongodb"，"sqljs"，"react-native"。此选项是**必需**的。
- `entities` - 要加载并用于此连接的实体。接受要加载的实体类和目录路径。目录支持 glob 模式。示例：`entities: [Post, Category, "entity/*.js", "modules/**/entity/*.js"]`。了解有关 [entities](https://typeorm.bootcss.com/entities) 的更多信息。
- `synchronize` - 指示是否在每次应用程序启动时自动创建数据库架构。 请注意此选项，不要在生产环境中使用它，否则将丢失所有生产数据。但是此选项在调试和开发期间非常有用。作为替代方案，你可以使用 CLI 运行 schema：sync 命令。请注意，对于 MongoDB 数据库，它不会创建模式，因为 MongoDB 是无模式的。相反，它只是通过创建索引来同步。

  > 试了一下，只会创建没有表，不会像 sequalize 的 force 那样删除再创建
  >
  > 如果我新增了一个列，此时会同步新增列，测试一下数据会怎么变化，只会新增一个列
  >
  > 如果我实体删除了一个列，此时同步数据就会丢失
  >
  > 如果我改变了一个列的信息，这个列也会消失，比如列的数据类型发生变化
  >
  > 如果我只是改变了表名，数据不会丢失

- `entitySchemas` - 要加载并用于此连接的实体架构。接受要加载的实体模式类和目录。目录支持 glob 模式。示例：`entitySchemas: [PostSchema, CategorySchema, "entity-schema/*.json"`。了解有关 [Entity Schemas](https://typeorm.bootcss.com/separating-entity-definition) 的更多信息。
- `dropSchema` - 每次建立连接时删除架构。请注意此选项，不要在生产环境中使用它，否则将丢失所有生产数据。但是此选项在调试和开发期间非常有用。
- `cache` - 启用实体结果缓存。你还可以在此处配置缓存类型和其他缓存选项。阅读更多有关 [caching](https://typeorm.bootcss.com/caching) 的信息。
- `name` - 连接名。 在使用 `getConnection(name: string)` 或 `ConnectionManager.get(name: string)` 时候需要用到。不同连接的连接名称不能相同，它们都必须是唯一的。如果没有给出连接名称，那么它将被设置为 "default"。
- `namingStrategy` - 命名策略，用于命名数据 库中的表和列。了解有关 [Naming strategies](https://typeorm.bootcss.com/naming-strategy) 的更多信息。
- `entityPrefix` - 给此数据库连接上的所有表（或集合）加的前缀。
- `cli.entitiesDir` - CLI 默认情况下创建实体的目录。
- `cli.migrationsDir` - CLI 默认情况下创建迁移的目录。
- `cli.subscribersDir` - CLI 默认情况下创建订阅者的目录。
- `extra` - 要传递给底层驱动程序的额外连接选项。如果要将额外设置传递给基础数据库驱动程序，请使用此配置。
- `subscribers` - 要加载并用于此连接的订阅者。接受要加载的实体类和目录。目录支持 glob 模式。示例：`subscribers: [PostSubscriber, AppSubscriber, "subscriber/*.js", "modules/**/subscriber/*.js"]`。了解有关 [subscribers](https://typeorm.bootcss.com/listeners-and-subscribers) 的更多信息。
- `migrations` - 要加载和用于此连接的迁移。接受要加载的迁移类和目录。目录支持 glob 模式。 示例: `migrations: [FirstMigration, SecondMigration, "migration/*.js", "modules/**/migration/*.js"]`. 了解有关 [Migrations](https://typeorm.bootcss.com/migrations) 的更多信息。
- `logging` - 指示是否启用日志记录。如果设置为 `true`，则将启用查询和错误日志记录。你还可以指定要启用的不同类型的日志记录，例如 `["query", "error", "schema"]`。详细了解 [Logging](https://typeorm.bootcss.com/logging)。
- `logger` - 记录器，用于日志的记录方式。可能的值是 "advanced-console", "simple-console" 和 "file"。默认为 "advanced-console"。你还可以指定实现 `Logger` 接口的记录器类。详细了解 [Logging](https://typeorm.bootcss.com/logging)。
- `maxQueryExecutionTime` - 如果查询执行时间超过此给定的最大执行时间（以毫秒为单位），则 logger 将记录此查询。
- `migrationsRun` - 指示是否在每次启动应用程序时自动运行迁移。或者，您可以使用 CLI run migrations:run command
- `migrationsTableName` - 数据库中将包含有关已执行迁移的信息的表的名称。默认情况下，此表名为 "migrations"。

### `mysql`/`mariadb`[#](https://typeorm.bootcss.com/connection-options#mysqlmariadb)

- `url` - 连接 URL
- `host` - 数据库 host
- `port` - 数据库端口。mysql 默认的端口是 `3306`.
- `username` - 数据库用户名
- `password` - 数据库密码
- `database` - 数据库名
- `charset` - 连接的字符集。这在 MySQL 的 SQL 级别中称为 "collation"（如 utf8_general_ci）。如果指定了 SQL 级别的字符集（如 utf8mb4），则使用该字符集的默认排序规则。 （默认值：UTF8_GENERAL_CI）。
- `timezone` - MySQL 服务器上配置的时区。这用于将服务器日期/时间值强制转换为 JavaScript Date 对象，反之亦然。该值可以是 `local`，`Z` 或 `+HHMM` 或 `-HHMM` 形式的偏移。 （默认：`local`）
- `connectTimeout` - 在连接到 MySQL 服务器期间发生超时之前的毫秒数。 （默认值：`10000`）
- `insecureAuth` - 允许连接到要求旧（不安全）身份验证方法的 MySQL 实例。 （默认值：`false`）
- `supportBigNumbers` - 处理数据库中的大数字（`BIGINT` 和 `DECIMAL` 列）时，应启用此选项（默认值：`false`）
- `bigNumberStrings` - 同时启用 `supportBigNumbers` 和 `bigNumberStrings` 会强制将大数字（`BIGINT` 和 `DECIMAL` 列）作为 JavaScript String 对象返回（默认值：`false`）。启用 `supportBigNumbers` 但禁用 `bigNumberStrings` 仅当它们无法用 JavaScript Number 对象准确表示时才会返回大数字作为 String 对象（当它们超过 `[-2^53，+2^53]` 范围时会发生），否则它们将被返回作为数字对象。如果禁用了 `supportBigNumbers`，则忽略此选项。
- `dateStrings` - 强制日期类型（`TIMESTAMP`，`DATETIME`，`DATE`）作为字符串返回，而不是转换为 JavaScript Date 对象。可以是 true/false 或要保留为字符串的类型名称数组。 （默认值：`false`）
- `debug` - 将协议详细信息打印到 stdout。可以是 true/false 或应打印的数据包类型名称数组。 （默认值：`false`）
- `trace` - 在 Error 上生成堆栈跟踪,包括库入口的调用站点（"long stack traces"）。对大多调用来说，性能损失很轻。 （默认值：`true`）
- `multipleStatements` - 每个查询允许多个 mysql 语句。请注意，它可能会增加 SQL 注入攻击的范围。 （默认值：`false`）
- `legacySpatialSupport` - Use spatial functions like GeomFromText and AsText which are removed in MySQL 8. (Default: true)
- `flags` - 使用非默认连接标志的连接标志列表。也可以将默认值列入黑名单。有关更多信息，请查看 [Connection Flags](https://github.com/mysqljs/mysql#connection-flags)。
- `ssl` - 带有 ssl 参数的对象或包含 ssl 配置文件名称的字符串。请参阅 [SSL 选项](https://github.com/mysqljs/mysql#ssl-options)。

## 多个连接，数据库，模式和主从复制设置

### 使用多个连接

```ts
import {createConnections} from "typeorm";

const connections = await createConnections([{
    name: "db1Connection",
    type: "mysql",
    host: "localhost",
    port: 3306,
    username: "root",
    password: "admin",
    database: "db1",
    entities: [__dirname + "/entity/*{.js,.ts}"],
    synchronize: true
}, {
    name: "db2Connection",
    type: "mysql",
    host: "localhost",
    port: 3306,
    username: "root",
    password: "admin",
    database: "db2",
    entities: [__dirname + "/entity/*{.js,.ts}"],
    synchronize: true
}]);
```

此方法允许你连接到已拥有的任意数量的数据库，每个数据库都有自己的配置，自己的实体和整体 ORM 范围和设置。

对于每个连接，将创建一个新的 `Connection` 实例。 你必须为创建的每个连接指定唯一的名称。

也可以从 ormconfig 文件加载所有连接选项：

```mysql
import {createConnections} from "typeorm";

const connections = await createConnections();
```

指定要按名称创建的连接：

```ts
import {createConnection} from "typeorm";

const connection = await createConnection("db2Connection");
```

使用连接时，必须指定连接名称以获取特定连接：

```ts
import {getConnection} from "typeorm";

const db1Connection = getConnection("db1Connection");
// 现在可以使用"db1"数据库...

const db2Connection = getConnection("db2Connection");
// 现在可以使用"db2"数据库...
```

使用此方法的好处是你可以使用不同的登录凭据，主机，端口甚至数据库类型来配置多个连接。

但是缺点可能是需要管理和使用多个连接实例。

### 在单个连接中使用多个数据库

数据库连接中指定的 database 字段作为主数据库，如果实体中不填 具体字段则默认是 database 字段。想要使用连接的 mysql 服务中的其他数据库，只要在实体上直接声明即可

如果你不想创建多个连接，但是想在一个连接中使用多个数据库，则可以指定使用的每个实体的数据库名称：

```ts
import {Entity, PrimaryGeneratedColumn, Column} from "typeorm";

@Entity({ database: "secondDB" })
export class User {

    @PrimaryGeneratedColumn()
    id: number;

    @Column()
    firstName: string;

    @Column()
    lastName: string;

}
```

```ts
import {Entity, PrimaryGeneratedColumn, Column} from "typeorm";

@Entity({ database: "thirdDB" })
export class Photo {

    @PrimaryGeneratedColumn()
    id: number;

    @Column()
    url: string;

}
```

`user` 实体将在 `secondDB` 数据库内创建，`Photo` 实体则在 `thirdDB` 数据库内。

如果要从其他数据库中选择数据，则只需提供一个实体：

```ts
const users = await connection
    .createQueryBuilder()
    .select()
    .from(User, "user")
    .addFrom(Photo, "photo")
    .andWhere("photo.userId = user.id")
    .getMany(); // userId因其跨数据库请求而不是外键
```

此代码将生成以下 sql 查询（取决于数据库类型）：

```mysql
SELECT * FROM "secondDB"."question" "question", "thirdDB"."photo" "photo" 
    WHERE "photo"."userId" = "user"."id"
```

还可以指定表而不是实体：

```ts
const users = await connection
    .createQueryBuilder()
    .select()
    .from("secondDB.user", "user")
    .addFrom("thirdDB.photo", "photo")
    .andWhere("photo.userId = user.id")
    .getMany(); // userId因其跨数据库请求而不是外键
```

# 实体

## 概述

实体是一个映射到数据库表的类。 你可以通过定义一个新类来创建一个实体，并用 `@Entity()` 来标记：

> 或使用 MongoDB 时的集合

```ts
import { Entity, PrimaryGeneratedColumn, Column } from "typeorm";

@Entity()
export class User {
    @PrimaryGeneratedColumn()
    id: number;

    @Column()
    firstName: string;

    @Column()
    lastName: string;

    @Column()
    isActive: boolean;
}
```

这将创建以下数据库表：

```
+-------------+--------------+----------------------------+
|                          user                           |
+-------------+--------------+----------------------------+
| id          | int(11)      | PRIMARY KEY AUTO_INCREMENT |
| firstName   | varchar(255) |                            |
| lastName    | varchar(255) |                            |
| isActive    | boolean      |                            |
+-------------+--------------+----------------------------+
```

基本实体由列和关系组成。 每个实体**必须**有一个主列（如果使用 MongoDB，则为 ObjectId 列）

每个实体都必须在连接选项中注册：

```ts
import { createConnection, Connection } from "typeorm";
import { User } from "./entity/User";

const connection: Connection = await createConnection({
    type: "mysql",
    host: "localhost",
    port: 3306,
    username: "test",
    password: "test",
    database: "test",
    entities: [User]
});
```

或者你可以指定包含所有实体的整个目录， 该目录下所有实体都将被加载：

```ts
import { createConnection, Connection } from "typeorm";

const connection: Connection = await createConnection({
    type: "mysql",
    host: "localhost",
    port: 3306,
    username: "test",
    password: "test",
    database: "test",
    entities: ["entity/*.js"]
});
```

### @Entity()

如果要为 `User` 实体使用替代表名，可以在 `@ Entity` 中指定：`@Entity（“my_users”）`

> 如果要为应用程序中的所有数据库表设置基本前缀，可以在连接选项中指定 `entityPrefix`。

使用实体构造函数时，其参数**必须是可选的**。 由于 ORM 在从数据库加载时才创建实体类的实例，因此在此之前并不知道构造函数的参数。

你还可以指定一些其他实体选项：

- `name` - 表名。 如果未指定，则从实体类名生成表名。
- `database` - 所选 DB 服务器中的数据库名称。
- `schema` - 架构名称。
- `engine` - 在表创建期间设置的数据库引擎（仅在某些数据库中有效）。
- `synchronize` - 架构更新中跳过标有 `false` 的实体。
- `skipSync` - 标有此装饰器的实体将从架构更新中跳过。
- `orderBy` - 使用 `find` 操作和 `QueryBuilder` 指定实体的默认排序。

例子:

```ts
@Entity({
    name: "users",
    engine: "MyISAM",
    database: 'example_dev',
    schema: 'schema_with_best_tables',
    synchronize: false,
    orderBy: {
        name: "ASC",
        id: "DESC"
    }
})
export class User {}
```

## 实体列

由于数据库表由列组成，因此实体也必须由列组成。 标有 `@ Column` 的每个实体类属性都将映射到数据库表列。

每个实体必须至少有一个主列。

### @PrimaryColumn()

`@PrimaryColumn()` 创建一个主列，它可以获取任何类型的任何值。你也可以指定列类型。 如果未指定列类型，则将从属性类型自动推断。

下面的示例将使用 `int` 类型创建 id，你必须在保存之前手动分配

```ts
import { Entity, PrimaryColumn } from "typeorm";

@Entity()
export class User {
    @PrimaryColumn()
    id: number;
}
```

### @PrimaryGeneratedColumn()

`@PrimaryGeneratedColumn()` 创建一个主列，该值将使用自动增量值自动生成。 它将使用 `auto-increment` /`serial` /`sequence` 创建 `int` 列（取决于数据库）。 你不必在保存之前手动分配其值，该值将会自动生成

### @PrimaryGeneratedColumn("uuid")

`@PrimaryGeneratedColumn("uuid")` 创建一个主列，该值将使用 `uuid` 自动生成。 Uuid 是一个独特的字符串 id。 你不必在保存之前手动分配其值，该值将自动生成。

```ts
import { Entity, PrimaryGeneratedColumn } from "typeorm";

@Entity()
export class User {
    @PrimaryGeneratedColumn("uuid")
    id: string;
}
```

### 复合主列

```ts
import { Entity, PrimaryColumn } from "typeorm";

@Entity()
export class User {
    @PrimaryColumn()
    firstName: string;

    @PrimaryColumn()
    lastName: string;
}
```

### 特殊列

有几种特殊的列类型可以使用：

- `@CreateDateColumn` 是一个特殊列，自动为实体插入日期。无需设置此列，该值将自动设置。
- `@UpdateDateColumn` 是一个特殊列，在每次调用实体管理器或存储库的 `save` 时，自动更新实体日期。无需设置此列，该值将自动设置。
- `@VersionColumn` 是一个特殊列，在每次调用实体管理器或存储库的 `save` 时自动增长实体版本（增量编号）。无需设置此列，该值将自动设置。

### 空间列 [#](https://typeorm.bootcss.com/entities#空间列)

## 列类型

TypeORM 支持所有最常用的数据库支持的列类型。 列类型是特定于数据库类型的 - 这为数据库架构提供了更大的灵活性。 你可以将列类型指定为 `@ Column` 的第一个参数 或者在 `@Column` 的列选项中指定，例如：

```ts
@Column("int")
```

```ts
@Column({ type: "int" })
```

```ts
@Column("varchar", { length: 200 })
```

```ts
@Column({ type: "int", length: 200 })
```

### `mysql`/`mariadb` 的列类型

`int`, `tinyint`, `smallint`, `mediumint`, `bigint`, `float`, `double`, `dec`, `decimal`, `numeric`, `date`, `datetime`, `timestamp`, `time`, `year`, `char`, `varchar`, `nvarchar`, `text`, `tinytext`, `mediumtext`, `blob`, `longtext`, `tinyblob`, `mediumblob`, `longblob`, `enum`, `json`, `binary`, `geometry`, `point`, `linestring`, `polygon`, `multipoint`, `multilinestring`, `multipolygon`, `geometrycollection`

### `enum` 列类型

`postgres` 和 `mysql` 都支持 `enum` 列类型。 并有多种列定义方式：

```ts
export enum UserRole {
    ADMIN = "admin",
    EDITOR = "editor",
    GHOST = "ghost"
}

@Entity()
export class User {

    @PrimaryGeneratedColumn()
    id: number;

    @Column({
        type: "enum",
        enum: UserRole,
        default: UserRole.GHOST
    })
    role: UserRole

}
```

### `simple-array` 的列类型 [#](https://typeorm.bootcss.com/entities#simple-array的列类型)

### `simple-json` 列类型 [#](https://typeorm.bootcss.com/entities#simple-json-列类型)

### 具有生成值的列

你可以使用 `@Generated` 装饰器创建具有生成值的列。 例如：

```ts
@Entity()
export class User {
    @PrimaryColumn()
    id: number;

    @Column()
    @Generated("uuid")
    uuid: string;
}
```

`uuid` 值将自动生成并存储到数据库中。

除了 "uuid" 之外，还有 "increment" 生成类型，但是对于这种类型的生成，某些数据库平台存在一些限制（例如，某些数据库只能有一个增量列，或者其中一些需要增量才能成为主键）。

## 列选项

列选项定义实体列的其他选项。 你可以在 `@ Column` 上指定列选项：

```ts
@Column({
    type: "varchar",
    length: 150,
    unique: true,
    // ...
})
name: string;
```

`ColumnOptions` 中可用选项列表：

- `type: ColumnType` - 列类型
- `name: string` - 数据库表中的列名。
- `length: number` - 列类型的长度。 例如，如果要创建 `varchar（150）` 类型，请指定列类型和长度选项。
- `width: number` - 列类型的显示范围。 仅用于 [MySQL integer types](https://dev.mysql.com/doc/refman/5.7/en/integer-types.html)
- `onUpdate: string` - `ON UPDATE` 触发器。 仅用于 [MySQL](https://dev.mysql.com/doc/refman/5.7/en/timestamp-initialization.html).
- `nullable: boolean` - 在数据库中使列 `NULL` 或 `NOT NULL`。 默认情况下，列是 `nullable：false`。
- `update: boolean` - 指示 "save" 操作是否更新列值。如果为 false，则只能在第一次插入对象时编写该值。 默认值为 "true"。
- `select: boolean` - 定义在进行查询时是否默认隐藏此列。 设置为 `false` 时，列数据不会显示标准查询。 默认情况下，列是 `select：true`
- `default: string` - 添加数据库级列的 `DEFAULT` 值。
- `primary: boolean` - 将列标记为主要列。 使用方式和 `@ PrimaryColumn` 相同。
- `unique: boolean` - 将列标记为唯一列（创建唯一约束）。
- `comment: string` - 数据库列备注，并非所有数据库类型都支持。
- `precision: number` - 十进制（精确数字）列的精度（仅适用于十进制列），这是为值存储的最大位数。仅用于某些列类型。
- `scale: number` - 十进制（精确数字）列的比例（仅适用于十进制列），表示小数点右侧的位数，且不得大于精度。 仅用于某些列类型。
- `zerofill: boolean` - 将 `ZEROFILL` 属性设置为数字列。 仅在 MySQL 中使用。 如果是 `true`，MySQL 会自动将 `UNSIGNED` 属性添加到此列。
- `unsigned: boolean` - 将 `UNSIGNED` 属性设置为数字列。 仅在 MySQL 中使用。
- `charset: string` - 定义列字符集。 并非所有数据库类型都支持。
- `collation: string` - 定义列排序规则。
- `enum: string[]|AnyEnum` - 在 `enum` 列类型中使用，以指定允许的枚举值列表。 你也可以指定数组或指定枚举类。
- `asExpression: string` - 生成的列表达式。 仅在 [MySQL](https://dev.mysql.com/doc/refman/5.7/en/create-table-generated-columns.html) 中使用。
- `generatedType: "VIRTUAL"|"STORED"` - 生成的列类型。 仅在 [MySQL](https://dev.mysql.com/doc/refman/5.7/en/create-table-generated-columns.html) 中使用。
- `hstoreType: "object"|"string"` - 返回 `HSTORE` 列类型。 以字符串或对象的形式返回值。 仅在 [Postgres](https://www.postgresql.org/docs/9.6/static/hstore.html)>) 中使用。
- `array: boolean` - 用于可以是数组的 postgres 列类型（例如 int []）
- `transformer: { from(value: DatabaseType): EntityType, to(value: EntityType): DatabaseType }` - 用于将任意类型 `EntityType` 的属性编组为数据库支持的类型 `DatabaseType`。

## 实体继承

你可以使用实体继承减少代码中的重复。

例如，你有 `Photo`, `Question`, `Post` 三个实体。所有这些实体都有共同的列：`id`，`title`，`description`。 为了减少重复并产生更好的抽象，我们可以为它们创建一个名为 `Content` 的基类。

其实就是普通的 JS 继承

## 树实体 [#](https://typeorm.bootcss.com/entities#树实体)

## 嵌入式实体

通过使用 `embedded columns`，可以减少应用程序中的重复（使用组合而不是继承）。

嵌入列是一个列，它接受具有自己列的类，并将这些列合并到当前实体的数据库表中。

例如：假设我们有 `User`，`Employee` 和 `Student` 实体。这些属性都有少量的共同点，`first name` 和 `last name` 属性。

```ts
import {Entity, PrimaryGeneratedColumn, Column} from "typeorm";

@Entity()
export class User {
    
    @PrimaryGeneratedColumn()
    id: string;
    
    @Column()
    firstName: string;
    
    @Column()
    lastName: string;
    
    @Column()
    isActive: boolean;
    
}
```

我们可以做的是通过创建一个包含 `firstName` 和 `lastName` 的新类：

```ts
import {Entity, Column} from "typeorm";

export class Name {
    
    @Column()
    first: string;
    
    @Column()
    last: string;
    
}
```

然后 "connect" 实体中的这些列：

```ts
import {Entity, PrimaryGeneratedColumn, Column} from "typeorm";
import {Name} from "./Name";

@Entity()
export class User {
    
    @PrimaryGeneratedColumn()
    id: string;
    
    @Column(type => Name)
    name: Name;
    
    @Column()
    isActive: boolean;
    
}
```

`Name` 实体中定义的所有列将合并为 `user`，`employee` 和 `student`：

```
+-------------+--------------+----------------------------+
|                          user                           |
+-------------+--------------+----------------------------+
| id          | int(11)      | PRIMARY KEY AUTO_INCREMENT |
| nameFirst   | varchar(255) |                            |
| nameLast    | varchar(255) |                            |
| isActive    | boolean      |                            |
+-------------+--------------+----------------------------+
```

这种方式可以减少实体类中的代码重复。 你可以根据需要在嵌入式类中使用尽可能多的列（或关系）。 甚至可以在嵌入式类中嵌套嵌套列。

## 分离实体定义

# 关系

在应用层处理就好

在数据库中，我的字段仅仅是一个 id，作为外键

而在 typeorm 中却是一个数组，这个是最奇怪的地方

加入我要查询一个关系表，我可以先拿到 id 外键，然后再根据这个 id 去关系表中查询

如果是 typeorm 难道是只要查到了 就可以从 数组中取？ 但是这样我就得自己去遍历数组了

## 疑问

typeId，在 chara 类型中，我是查询到 typeId 再直接插入的

如果使用了 MantToOne 能做到我不查询 id 吗？ 比如 客户端的语义化输入是 type live，我直接 insert live，能否自动转换成 typeId

不行的，关系是 Entity 之间的关系，live 只是一个字符串，框架没有处理，所以还是需要根据字符串查询出 EventType 实体，再与实体建立关系，所以定义实体的时候也只能写 外键列，写其他列是没有意义的

然后 groupEvent ，能否做到我直接更新 groupEvent ，然后 event-list 也一起更新？

# 操作实体

## Repository

`Repository` 就像 `EntityManager` 一样，但其操作仅限于具体实体。

你可以通过 `getRepository（Entity）`，`Connection＃getRepository` 或 `EntityManager＃getRepository` 访问存储库。

```ts
import { getRepository } from "typeorm";
import { User } from "./entity/User";

const userRepository = getRepository(User); // 你也可以通过getConnection().getRepository()或getManager().getRepository() 获取
const user = await userRepository.findOne(1);
user.name = "Umed";
await userRepository.save(user);
```

有三种类型的存储库：

- `Repository` - 任何实体的常规存储库。
- `TreeRepository` - 用于树实体的 `Repository` 的扩展存储库（比如标有 `@ Tree` 装饰器的实体）。有特殊的方法来处理树结构。
- `MongoRepository` - 具有特殊功能的存储库，仅用于 MongoDB。

## Repository API

- `manager` - 存储库使用的 `EntityManager`。

```ts
const manager = repository.manager;
```

- `metadata` - 存储库管理的实体的 `EntityMetadata`。 更多关于 [实体元数据中的事务](https://typeorm.bootcss.com/entity-metadata).
- `queryRunner` - `EntityManager` 使用的查询器。仅在 EntityManager 的事务实例中使用。
- `target` - 此存储库管理的目标实体类。仅在 EntityManager 的事务实例中使用。

```ts
const target = repository.target;
```

- `createQueryBuilder` - 创建用于构建 SQL 查询的查询构建器。 更多关于 [QueryBuilder](https://typeorm.bootcss.com/select-query-builder).
- `hasId` - 检查是否定义了给定实体的主列属性。

```ts
if (repository.hasId(user)) {
  // ... do something
}
```

- `getId` - 获取给定实体的主列属性值。复合主键返回的值将是一个具有主列名称和值的对象。

```ts
const userId = repository.getId(user); // userId === 1
```

## EntityManager

使用 `EntityManager`，你可以管理（insert, update, delete, load 等）任何实体。`EntityManager` 就像放一个实体存储库的集合的地方。

你可以通过 `getManager（）` 或 `Connection` 访问实体管理器。

如何使用它：

```ts
import { getManager } from "typeorm";
import { User } from "./entity/User";

const entityManager = getManager(); // 你也可以通过 getConnection().manager 获取
const user = await entityManager.findOne(User, 1);
user.name = "Umed";
await entityManager.save(user);
```

## EntityManager API

## 对比

`EntityManager` 是数据库维度的，每次执行增删改查的时候，需要先获取 `repository` 再进行操作

> 我觉得简单理解为获取 repository 再操作就可以了

当多个实体可以复用相同的服务的时候，应该使用 `EntityManager` 进行实体操作

# 增删改查（Repository API）

`TypeORM` 支持存储库设计模式，因此每个实体都有自己的存储库。可以从数据库连接获得这些存储库。

存储库是 `TypeORM` 中增删改查的主体

## 增

### Insert

`insert` - 插入新实体或实体数组

```ts
await repository.insert({
  firstName: "Timber",
  lastName: "Timber"
});

await manager.insert(User, [
  {
    firstName: "Foo",
    lastName: "Bar"
  },
  {
    firstName: "Rizz",
    lastName: "Rak"
  }
]);
```

### Create + Save

`create` - 创建 `User` 的新实例。 接受具有用户属性的对象文字，该用户属性将写入新创建的用户对象（可选）。

```ts
const user = repository.create({
  id: 1,
  firstName: "Timber",
  lastName: "Saw"
}); 
// 等价于
const user =repository.create();
use.firstName = "Timber";
user.lastName = "Saw"''
```

> create 方法只是创建了一个实例，并没有更改数据库对应的表

`save` - 保存给定实体或实体数组：

+ 如果该实体已存在于数据库中，则会更新该实体。
+ 如果数据库中不存在该实体，则会插入该实体。
+ 它将所有给定实体保存在单个事务中（在实体的情况下，管理器不是事务性的）。
+ 因为跳过了所有未定义的属性，还支持部分更新。

```ts
await repository.save(user);
await repository.save([category1, category2, category3]);
```

### Merge + Save

`merge` - 将多个实体合并为一个实体。

```ts
const user = new User();
repository.merge(user, { firstName: "Timber" }, { lastName: "Saw" }); 
// 和 user.firstName = "Timber"; user.lastName = "Saw";一样
```

```ts
const newGroup = await this.groupsRepository.create(createGroupDto);
const test = this.groupsRepository.merge(newGroup, {
  members: ['honoka', 'kotori'],
});
console.log('test: ', test);
await this.groupsRepository.save(newGroup);
```

> test: Group { groupName: 'muse', members: [ 'honoka', 'kotori' ] }

### Preload + Save

`preload` - 从给定的普通 javascript 对象创建一个新实体。 如果实体已存在于数据库中，则它将加载它（以及与之相关的所有内容），并将所有值替换为给定对象中的新值，并返回新实体。 新实体实际上是从数据库加载的所有属性都替换为新对象的实体。

```ts
const partialUser = {
  id: 1,
  firstName: "Rizzrak",
  profile: {
    id: 1
  }
};
const user = await repository.preload(partialUser);
// user将包含partialUser中具有partialUser属性值的所有缺失数据：
// { id: 1, firstName: "Rizzrak", lastName: "Saw", profile: { id: 1, ... } }
```

## 查

### findOne

`findOne` - 查找匹配某些 ID 或查找选项的第一个实体。

```ts
const user = await repository.findOne(1);
const timber = await repository.findOne({ firstName: "Timber" });
```

### findByIds

`findByIds` - 按 ID 查找多个实体。

```ts
const users = await repository.findByIds([1, 2, 3]);
```

```ts
// 使用单个主键查找一个id
const person = await connection.manager.findOne(Person, 1);
const person = await connection.getRepository(Person).findOne(1);

// 使用复合主键找到一个id
const user = await connection.manager.findOne(User, { firstName: "Timber", lastName: "Saw" });
const user = await connection.getRepository(User).findOne({ firstName: "Timber", lastName: "Saw" });
```

### findOneOrFail

`findOneOrFail` - - `findOneOrFail` - 查找匹配某些 ID 或查找选项的第一个实体。 如果没有匹配，则 Rejects 一个 promise。

```ts
const user = await repository.findOneOrFail(1);
const timber = await repository.findOneOrFail({ firstName: "Timber" });
```

### Find

`find` - 查找指定条件的实体。

```ts
const timbers = await repository.find({ firstName: "Timber" });
```

不指定条件时，相当于 findAll，返回一个实体数组

```ts
group:  [
  Group { groupName: 'muse', members: [ 'honoka' ] },
  Group { groupName: 'aqours', members: [ 'string' ] }
]
```

### Count

`count` - 符合指定条件的实体数量。对分页很有用。

```ts
onst count = await repository.count({ firstName: "Timber" });
```

### findAndCount

`findAndCount` - 查找指定条件的实体。还会计算与给定条件匹配的所有实体数量， 但是忽略分页设置 (`skip` 和 `take` 选项)。

```ts
const [timbers, timbersCount] = await repository.findAndCount({ firstName: "Timber" });
```

### Query

`query` - 执行原始 SQL 查询。

```ts
onst rawData = await repository.query(`SELECT * FROM USERS`);
```

## 查询选项

所有存储库和管理器 `find` 方法都接受可用于查询所需数据的特殊选项，而无需使用 `QueryBuilder`

### Where

查询实体的简单条件

```ts
userRepository.find({ where: { firstName: "Timber", lastName: "Saw" } });
```

查询嵌入实体列应该根据定义它的层次结构来完成。 例：

```ts
userRepository.find({ where: { name: { first: "Timber", last: "Saw" } } });
```

使用 OR 运算符查询：

```ts
userRepository.find({
    where: [{ firstName: "Timber", lastName: "Saw" }, { firstName: "Stan", lastName: "Lee" }]
});
```

将执行以下查询：

```ts
SELECT * FROM "user" WHERE ("firstName" = 'Timber' AND "lastName" = 'Saw') OR ("firstName" = 'Stan' AND "lastName" = 'Lee')
```

### Order

选择排序

```ts
userRepository.find({
    order: {
        name: "ASC",
        id: "DESC"
    }
});
```

### OR 运算符

使用 OR 运算符查询：

```ts
userRepository.find({
    where: [{ firstName: "Timber", lastName: "Saw" }, { firstName: "Stan", lastName: "Lee" }]
});
```

将执行以下查询：

```sql
SELECT * FROM "user" WHERE ("firstName" = 'Timber' AND "lastName" = 'Saw') OR ("firstName" = 'Stan' AND "lastName" = 'Lee')
```

### 其他

`select` - 表示必须选择对象的哪些属性

```ts
userRepository.find({ select: ["firstName", "lastName"] });
```

`relations` - 关系需要加载主体。 也可以加载子关系（join 和 leftJoinAndSelect 的简写）

```ts
userRepository.find({ relations: ["profile", "photos", "videos"] });
userRepository.find({ relations: ["profile", "photos", "videos", "videos.video_attributes"] });
```

`join` - 需要为实体执行联接，扩展版对的 "relations"。

```ts
userRepository.find({
    join: {
        alias: "user",
        leftJoinAndSelect: {
            profile: "user.profile",
            photo: "user.photos",
            video: "user.videos"
        }
    }
});
```

`cache` - 启用或禁用查询结果缓存。 有关更多信息和选项，请参见 [caching](https://typeorm.bootcss.com/caching)。

```ts
userRepository.find({
    cache: true
});
```

`lock` - 启用锁查询。 只能在 `findOne` 方法中使用。 `lock` 是一个对象，可以定义为：

```ts
{ mode: "optimistic", version: number|Date }
```

```ts
{ mode: "pessimistic_read"|"pessimistic_write"|"dirty_read" }
```

```ts
userRepository.findOne(1, {
    lock: { mode: "optimistic", version: 1 }
})
```

### 分页

返回多个实体的 `find` 方法（`find`，`findAndCount`，`findByIds`），同时也接受以下选项：

`skip` - 偏移（分页）

```ts
userRepository.find({
    skip: 5
});
```

`take` - limit (分页) - 得到的最大实体数，相当于是 limit

```ts
userRepository.find({
    take: 10
});
```

如果你正在使用带有 MSSQL 的 typeorm，并且想要使用 `take` 或 `limit`，你必须正确使用 order，否则将会收到以下错误：'FETCH 语句中 NEXT 选项的使用无效。'

```ts
userRepository.find({
    order: {
        columnName: "ASC"
    },
    skip: 0,
    take: 10
});
```

### 完整示例

```ts
userRepository.find({
    select: ["firstName", "lastName"],
    relations: ["profile", "photos", "videos"],
    where: {
        firstName: "Timber",
        lastName: "Saw"
    },
    order: {
        name: "ASC",
        id: "DESC"
    },
    skip: 5,
    take: 10,
    cache: true
});
```

### 进阶选项

TypeORM 提供了许多内置运算符，可用于创建更复杂的查询

+ `Not`：not 运算符还可以与其他运算符组合

```ts
import { Not } from "typeorm";

const loadedPosts = await connection.getRepository(Post).find({
    title: Not("About #1")
});
```

```sql
SELECT * FROM "post" WHERE "title" != 'About #1'
```

- `LessThan`

```ts
import { LessThan } from "typeorm";

const loadedPosts = await connection.getRepository(Post).find({
    likes: LessThan(10)
});
```

```sql
SELECT * FROM "post" WHERE "likes" < 10
```

- `LessThanOrEqual`

```ts
import { LessThanOrEqual } from "typeorm";
const loadedPosts = await connection.getRepository(Post).find({
    likes: LessThanOrEqual(10)
});
```

```sql
SELECT * FROM "post" WHERE "likes" <= 10
```

- `MoreThan`
- `MoreThanOrEqual`

+ `Equal`
+ `Between`

```ts
import { Between } from "typeorm";

const loadedPosts = await connection.getRepository(Post).find({
    likes: Between(1, 10)
});
```

```ts
SELECT * FROM "post" WHERE "likes" BETWEEN 1 AND 10
```

- `Like`

```ts
import { Like } from "typeorm";

const loadedPosts = await connection.getRepository(Post).find({
    title: Like("%out #%")
});
```

```sql
SELECT * FROM "post" WHERE "title" LIKE '%out #%'
```

- `ILike`

```sql
SELECT * FROM "post" WHERE "title" ILIKE '%out #%'
```

- `In`

```ts
import { In } from "typeorm";

const loadedPosts = await connection.getRepository(Post).find({
    title: In(["About #2", "About #3"])
});
```

```sql
SELECT * FROM "post" WHERE "title" IN ('About #2','About #3')
```

- `Any`

```ts
import { Any } from "typeorm";

const loadedPosts = await connection.getRepository(Post).find({
    title: Any(["About #2", "About #3"])
});
```

```sql
SELECT * FROM "post" WHERE "title" = ANY(['About #2','About #3'])
```

- `IsNull`

```sql
SELECT * FROM "post" WHERE "title" IS NULL
```

- `Raw`

```ts
import { Raw } from "typeorm";

const loadedPosts = await connection.getRepository(Post).find({
    likes: Raw("1 + likes = 4")
});
```

```sql
SELECT * FROM "post" WHERE 1 + "likes" = 4
```

注意：注意 `Raw` 操作符。 它应该从提供的表达式执行纯 SQL，而不能包含用户输入，否则将导致 SQL 注入。

你还可以将这些运算符与 `Not` 运算符组合使用：

```ts
import { Not, MoreThan, Equal } from "typeorm";

const loadedPosts = await connection.getRepository(Post).find({
    likes: Not(MoreThan(10)),
    title: Not(Equal("About #2"))
});
```

```sql
SELECT * FROM "post" WHERE NOT("likes" > 10) AND NOT("title" = 'About #2')
```

## 原始查询

```ts
    async findOneProjectRecord({ date, projectName, recordType }: QueryOneProjectRecordDto) {
        const repository = this.getRepositoryByProject(projectName);
        const { recordTypeId } = await this.recordTypeRepository.findOne({
            where: { name: 'twitter_follower' },
        });
        const projectRecord = await repository.query(`
            SELECT
                '${date}' as date,
                GROUP_CONCAT(record ORDER BY member_id SEPARATOR ',') as records
            FROM canon_record.llss_seiyuu
            WHERE date = ?
            GROUP BY date;
        `, [date]);
        return projectRecord[0];
    }
```

通过占位符传入参数，多个占位符顺次接受数组

占位符参考：https://github.com/mysqljs/mysql#escaping-query-values

各个数据库有不同的规则

https://github.com/typeorm/typeorm/issues/881

## 改

### Update

`update` - 通过给定的更新选项或实体 ID 部分更新实体

其实是 findAndUpdate

**参数：**

1. criteria 查询参数，id，ids
2. updateDto

**返回值：**

1. 没看懂，怪怪的

```ts
await repository.update({ firstName: "Timber" }, { firstName: "Rizzrak" });
// 执行 UPDATE user SET firstName = Rizzrak WHERE firstName = Timber

await repository.update(1, { firstName: "Rizzrak" });
// 执行 UPDATE user SET firstName = Rizzrak WHERE id = 1
```

### Increment

`increment` - 增加符合条件的实体某些列值。

```ts
await manager.increment(User, { firstName: "Timber" }, "age", 3);
```

### Decrement

`decrement` - 减少符合条件的实体某些列值。

```ts
await manager.decrement(User, { firstName: "Timber" }, "age", 3);
```

## 删

### Remove

`remove` - 删除给定的实体或实体数组。

它将删除单个事务中的所有给定实体（在实体的情况下，管理器不是事务性的）。

```ts
await repository.remove(user);
await repository.remove([category1, category2, category3]);
```

### Delete

`delete` - 根据实体 id, ids 或给定的条件删除实体：

```ts
await repository.delete(1);
await repository.delete([1, 2, 3]);
await repository.delete({ firstName: "Timber" });
```

### Clear

`clear` - 清除给定表中的所有数据 (truncates/drops)。

```ts
await repository.clear();
```

# 扩展 repository[#](https://typeorm.bootcss.com/custom-repository)

# 日志

## 开启日志

你只需在连接选项中设置 `logging：true` 即可启用所有查询和错误的记录：

```js
{
    name: "mysql",
    type: "mysql",
    host: "localhost",
    port: 3306,
    username: "test",
    password: "test",
    database: "test",
    ...
    logging: true
}
```

## 日志选项

可以在连接选项中启用不同类型的日志记录：

```js
{
    host: "localhost",
    ...
    logging: ["query", "error"]
}
```

如果要启用失败查询的日志记录，则只添加 `error`：

```js
{
    host: "localhost",
    ...
    logging: ["error"]
}
```

还可以使用其他选项：

- `query` - 记录所有查询。
- `error` - 记录所有失败的查询和错误。
- `schema` - 记录架构构建过程。
- `warn` - 记录内部 orm 警告。
- `info` - 记录内部 orm 信息性消息。
- `log` - 记录内部 orm 日志消息。

你可以根据需要指定任意数量的选项。 如果要启用所有日志记录，只需指定 `logging：“all”`：

## 记录耗时长的查询

如果遇到性能问题，可以通过在连接选项中设置 `maxQueryExecutionTime` 来记录执行时间过长的查询：

```
{
    host: "localhost",
    ...
    maxQueryExecutionTime: 1000
}
```

此代码将记录所有运行超过 `1秒` 的查询。

# 更改默认记录器

TypeORM 附带 4 种不同类型的记录器：

- `advanced-console` - 默认记录器，它将使用颜色和 sql 语法高亮显示所有记录到控制台中的消息（使用 [chalk](https://github.com/chalk/chalk)）。
- `simple-console` - 简单的控制台记录器，与高级记录器完全相同，但它不使用任何颜色突出显示。

  如果你不喜欢/或者使用彩色日志有问题，可以使用此记录器。

- `file` - 这个记录器将所有日志写入项目根文件夹中的 `ormlogs.log`（靠近 `package.json` 和 `ormconfig.json`）。
- `debug` - 此记录器使用 [debug package](https://github.com/visionmedia/debug) 打开日志记录设置你的 env 变量 `DEBUG = typeorm：*`（注意记录选项对此记录器没有影响）。

你可以在连接选项中启用其中任何一个：

```
{
    host: "localhost",
    ...
    logging: true,
    logger: "file"
}
```

## 使用自定义记录器

你可以通过实现 `Logger` 接口来创建自己的记录器类：

```ts
import { Logger } from 'typeorm';

export class MyCustomLogger implements Logger {
    // 实现logger类的所有方法
}
```

并在连接选项中指定它：

```ts
import { createConnection } from 'typeorm';
import { MyCustomLogger } from './logger/MyCustomLogger';

createConnection({
    name: 'mysql',
    type: 'mysql',
    host: 'localhost',
    port: 3306,
    username: 'test',
    password: 'test',
    database: 'test',
    logger: new MyCustomLogger(),
});	
```

如果在 `ormconfig` 文件中定义了 然后你可以使用它并以下面的方式覆盖它：

```ts
import { createConnection, getConnectionOptions } from "typeorm";
import { MyCustomLogger } from "./logger/MyCustomLogger";

// getConnectionOptions将从ormconfig文件中读取选项并将其返回到connectionOptions对象中，
// 然后你只需向其附加其他属性
getConnectionOptions().then(connectionOptions => {
  return createConnection(
    Object.assign(connectionOptions, {
      logger: new MyCustomLogger()
    })
  );
});
```

记录器方法可接受 `QueryRunner`。 如果要记录其他数据将会很有帮助。 此外，通过查询运行程序，你可以访问在持久/删除期间传递的其他数据。 例如：

```ts
// 用户在实体保存期间发送请求
postRepository.save(post, { data: { request: request } });

// 在logger中你可以这样访问它：
logQuery(query: string, parameters?: any[], queryRunner?: QueryRunner) {
    const requestUrl = queryRunner && queryRunner.data["request"] ? "(" + queryRunner.data["request"].url + ") " : "";
    console.log(requestUrl + "executing query: " + sql);
}
```
