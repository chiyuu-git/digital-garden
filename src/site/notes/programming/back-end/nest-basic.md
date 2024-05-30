---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2022-09-03-Sat, 10:08:31 pm","date-modified":"2023-04-29-Sat, 8:19:43 pm","permalink":"/programming/back-end/nest-basic/","dgPassFrontmatter":true}
---


后端习惯于用类的方式定义数据库中的每一张表

graphql 和 severless 的关系？没什么关系，感觉 graphql 用途还是有限，还是继续学 restful 吧

https://github.com/nestjs/nest

https://docs.nestjs.cn/6/recipes?id=sql-typeorm

https://zhuanlan.zhihu.com/p/28621374

https://www.cnblogs.com/nayek/p/12917504.html

https://www.bilibili.com/video/BV1U441117xK?p=8&spm_id_from=pageDriver

puppeteer

https://search.bilibili.com/all?keyword=Puppeteer&from_source=nav_search_new&order=totalrank&duration=0&tids_1=0

https://try-puppeteer.appspot.com/

https://zhuanlan.zhihu.com/p/76237595

https://github.com/puppeteer/puppeteer/blob/main/docs/api.md

https://www.jeffjade.com/2017/12/17/134-kinds-of-toss-using-puppeteer/

可以把监控过程变成 serverless 的？ 我整一个 qq 群机器人，给群友使用就可以了，但是国内的 ip 没有办法访问 twitter 啊

教程：https://zhuanlan.zhihu.com/p/73862674

https://zhuanlan.zhihu.com/p/32536137

https://zhuanlan.zhihu.com/p/58877583

# Nestjs + Typeorm + Mysql

中文文档：https://docs.nestjs.cn/8/techniques?id=%e6%95%b0%e6%8d%ae%e5%ba%93

## 基本介绍

```
npm i -g @nestjs/cli
```

### 项目结构

| `app.controller.ts`      | 带有单个路由的基本控制器。                                   |
| ------------------------ | ------------------------------------------------------------ |
| `app.controller.spec.ts` | 针对控制器的单元测试。                                       |
| `app.module.ts`          | T 应用程序的根模块（root module）。                           |
| `app.service.ts`         | 具有单一方法的基本服务（service）。 method.                  |
| `main.ts`                | 应用程序的入口文件，它使用核心函数 `NestFactory` 来创建 Nest 应用程序的实例。 |

`main.ts` 文件中包含了一个异步函数，此函数将 **引导（bootstrap）** 应用程序的启动过程：

```TS
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  await app.listen(3000);
}
bootstrap();
```

`NestFactory` 核心类暴露了一些静态方法用于创建应用程序的实例。

其中，`create()` 方法返回一个应用程序的对象，该对象实现了 `INestApplication` 接口。该对象还提供了一组方法，这些方法将在接下来的章节中进行介绍。

在上面的 `main.ts` 示例中，我们仅启动了 HTTP 侦听器，该侦听器使应用程序可以侦听入栈的 HTTP 请求。

### 平台

Nest 的目标是成为一个与平台无关的框架。平台独立性使创建可重用的逻辑部分成为可能，开发人员可以在多种不同类型的应用程序中利用这些逻辑部分。 从技术上讲，一旦创建了适配器，Nest 便可以使用任何 Node HTTP 框架。 目前支持两个 HTTP 平台：[express](https://expressjs.com/) 和 [fastify](https://www.fastify.io/)。 您可以根据您的需求选择最适合平台。

|                    |                                                              |
| ------------------ | ------------------------------------------------------------ |
| `platform-express` | [Express](https://expressjs.com/) 是一个著名的、极简的、专为 node 开发的 web 框架。它久经考验、适用于生产环境的软件库，并且拥有大量的社区资源。默认情况下使用 `@nestjs/platform-express` 软件包。许多用户对 Express 都很满意，并且无需采取任何操作即可启用它。 |
| `platform-fastify` | [Fastify](https://www.fastify.io/) 是一个高性能且低开销的框架，高度专注于提供最高的效率和速度。点击 [这里](https://nestjs.bootcss.com/techniques/performance) 查看它的使用文档。 |

无论使用那个平台，都会将平台的 application 接口暴露出来。它们分别是 `NestExpressApplication` 和 `NestFastifyApplication`。

当您将类型信息传递给 `NestFactory.create()` 方法时，如下例所示，`app` 对象将具有该特定平台的专用方法。但是请注意，**除非** 您确实需要访问底层平台的 API，否则 **无需** 指定平台类型。

```TS
const app = await NestFactory.create<NestExpressApplication>(AppModule);
```

# 控制器

控制器负责处理传入的**请求**和向客户端返回**响应**。

控制器的目的是接收应用的特定请求。**路由**机制控制哪个控制器接收哪些请求。通常，每个控制器有多个路由，不同的路由可以执行不同的操作。

为了创建一个基本的控制器，我们使用类和 `装饰器`。装饰器将类与所需的元数据相关联，并使 Nest 能够创建路由映射（将请求绑定到相应的控制器）。

## 路由

### 基础示例

在下面的例子中，我们使用 `@Controller()` 装饰器定义一个基本的控制器。路由路径前缀设置为 `cats`

在 `@Controller()` 装饰器中使用路径前缀地对一组相关的路由进行分组，并最大程度地减少重复代码

例中，我们将一组用于管理 `/cats` 下的客户实体的路由进行分组。我们在 `@Controller()` 装饰器中指定路径前缀 `cats`，这样就不必为文件中的每个路由重复路径的那部分。

> `@Controller()` 装饰器指定的路径，作为**根路由路径**

```typescript
/* cats.controller.ts */

import { Controller, Get } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Get()
  findAll(): string {
    return 'This action returns all cats';
  }
}
```

`findAll()` 方法之前的 `@Get()` HTTP 请求方法装饰器告诉 Nest 为 HTTP 请求的特定端点创建处理程序

端点对应于 `HTTP` 请求方法（在本例中为 GET ）和路由路径（如 `GET /customer` ）

### 路由路径

什么是路由路径 ？ 一个处理程序的路由路径是通过连接为控制器 （Controller） 声明的（可选）前缀和请求装饰器中指定的任何路径来确定的。

由于我们已经为每个 `route（cats`） 声明了一个前缀，并且没有在装饰器中添加任何路由信息，因此 Nest 会将 `GET /cats` 请求映射到此处理程序

在上面的示例中，当对此端点发出 `GET` 请求时， Nest 会将请求路由到我们的自定义的 `findAll()` 方法

### HTTP 请求方法装饰器

我们已经创建了一个端点来获取 cats 的数据（**GET** 路由）。我们通常还希望提供一个创建新记录的端点。为此，让我们创建 **POST** 处理程序:

```typescript
/* cats.controller.ts */

import { Controller, Get, Post } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Post()
  create(): string {
    return 'This action adds a new cat';
  }

  @Get()
  findAll(): string {
    return 'This action returns all cats';
  }
}
```

就这么简单。 Nest 为所有标准的 HTTP 方法提供了相应的装饰器：`@Put()`、`@Delete()`、`@Patch()`、`@Options()`、以及 `@Head()`。此外，`@All()` 则用于定义一个用于处理所有 HTTP 请求方法的处理程序。

### 路由通配符

路由同样支持模式匹配。例如，星号被用作通配符，将匹配任何字符组合。

```typescript
@Get('ab*cd')
findAll() {
  return 'This route uses a wildcard';
}
```

路由路径 `'ab*cd'` 将匹配 `abcd` 、`ab_cd` 、`abecd` 等。字符 `?` 、`+` 、 `*` 以及 `()` 是它们的正则表达式对应项的子集。连字符（`-`） 和点（`.`）按字符串路径逐字解析。

### 路由参数

当您需要接受**动态数据**（dynamic data）作为请求的一部分时（例如，使用 `GET /cats/1` 来获取 id 为 `1` 的 `cat`），带有静态路径的路由将无法工作。

为了定义带参数的路由，我们可以在路由路径中添加路由参数**标记**（token）以捕获请求 URL 中该位置的动态值。

下面的 `@Get()` 装饰器示例中的路由参数标记（route parameter token）演示了此用法。以这种方式声明的路由参数可以使用 `@Param()` 装饰器访问，该装饰器应添加到函数签名中。

```typescript
@Get(':id')
findOne(@Param() params): string {
  console.log(params.id);
  return `This action returns a #${params.id} cat`;
}
```

`@Param()` 用于修饰一个方法的参数（上面示例中的 `params`），并在该方法内将**路由参数**作为被修饰的方法参数的属性。如上面的代码所示，我们可以通过引用 `params.id` 来访问（路由路径中的） `id` 参数。 您还可以将特定的参数标记传递给装饰器，然后在方法主体中按参数名称直接引用路由参数。

`Param` 需要从 `@nestjs/common` 包导入。

```typescript
@Get(':id')
findOne(@Param('id') id): string {
  return `This action returns a #${id} cat`;
}
```

## 子域路由

`@Controller` 装饰器可以接受一个 `host` 选项，以要求传入请求的 `HTTP` 主机匹配某个特定值。

```typescript
@Controller({ host: 'admin.example.com' })
export class AdminController {
  @Get()
  index(): string {
    return 'Admin page';
  }
}
```

由于 **Fastify** 缺乏对嵌套路由器的支持，因此当使用子域路由时，应该改用（默认） **Express** 适配器（Express adapter）。

与一个路由路径 `path` 类似，该 `hosts` 选项可以使用参数标识（token）来捕获主机名中该位置的动态值。下面的 `@Controller()` 装饰器示例中的主机参数标识（host parameter token）演示了此用法。可以使用 `@HostParam()` 装饰器访问以这种方式声明的主机参数，该装饰器应添加到方法签名中。

```typescript
@Controller({ host: ':account.example.com' })
export class AccountController {
  @Get()
  getInfo(@HostParam('account') account: string) {
    return account;
  }
Copy to clipboardErrorCopied
```

## Request

处理程序有时需要访问客户端的**请求**细节。Nest 提供了对底层平台（默认为 `Express`）的 [**请求对象**](http://expressjs.com/en/api.html#req)（`request`）的访问方式。我们可以在处理函数的签名中使用 `@Req()` 装饰器，指示 Nest 将请求对象注入处理程序。

```typescript
/* cats.controller.ts */

import { Controller, Get, Req } from '@nestjs/common';
import { Request } from 'express';

@Controller('cats')
export class CatsController {
  @Get()
  findAll(@Req() request: Request): string {
    return 'This action returns all cats';
  }
}
```

> 为了在 `express` 中使用 `Typescript` （如 `request: Request` 上面的参数示例所示），请安装 `@types/express` 。

`Request` 对象代表 `HTTP` 请求，并具有查询字符串，请求参数参数，HTTP 标头（HTTP header） 和 正文（HTTP body）的属性（在 [这里](https://expressjs.com/en/api.html#req) 阅读更多）。在多数情况下，不必手动获取它们。 我们可以使用专用的装饰器，比如开箱即用的 `@Body()` 或 `@Query()` 。 下面是 Nest 提供的装饰器及其代表的底层平台特定对象的对照列表。

|                           |                                   |
| :------------------------ | :-------------------------------- |
| `@Request()，@Req()`      | `req`                             |
| `@Response()，@Res()*`    | `res`                             |
| `@Next()`                 | `next`                            |
| `@Session()`              | `req.session`                     |
| `@Param(key?: string)`    | `req.params`/`req.params[key]`    |
| `@Body(key?: string)`     | `req.body`/`req.body[key]`        |
| `@Query(key?: string)`    | `req.query`/`req.query[key]`      |
| `@Headers(name?: string)` | `req.headers`/`req.headers[name]` |
| `@Ip()`                   | `req.ip`                          |
| `@HostParam()`            | `req.hosts`                       |

为了与底层 HTTP 平台（例如，`Express` 和 `Fastify`）之间的类型兼容， Nest 提供了 `@Res()` 和 `@Response()` 装饰器。`@Res()` 只是 `@Response()` 的别名。两者都直接暴露了底层平台的 `response` 对象接口。在使用它们时，您还应该导入底层库的类型声明（如：`@types/express`）以充分利用它们。需要注意的是，在请求处理函数中注入 `@Res()` 或 `@Response()` 时，会将 Nest 置于该处理函数的**特定于库**（Library-specific mode）的模式下，并负责管理响应。这样做时，必须通过调用 `response` 对象（例如，`res.json(…)` 或 `res.send(…)`）发出某种响应，否则 HTTP 服务器将挂起。

### 多个路由参数如何实现？

## Response

 `findAll()` 方法将返回 `200` 状态代码和相关的响应，在本例中只返回了一个字符串。为什么会这样？ 为了解释原因，首先我们将介绍 Nest 使用两种不同的操作响应选项的概念：

|              |                                                              |
| ------------ | :----------------------------------------------------------- |
| 标准（推荐） | 使用这个内置方法，当请求处理程序返回一个 `JavaScript` 对象或数组时，它将自动序列化为 `JSON`。但是，当它返回一个 `JavaScript` 基本类型（例如 `string、number、boolean`）时， Nest 将只发送值，而不尝试序列化它。这使响应处理变得简单：只需要返回值，其余的由 Nest 负责。 |
| 类库特有的   | 我们可以在函数签名处通过 `@Res()` 注入类库特定的响应对象（例如， `Express`）。使用此方法，你就能使用由该响应对象暴露的原生响应处理函数。例如，使用 `Express`，您可以使用 `response.status(200).send()` 构建响应 |

注意：Nest 检测处理程序何时使用 `@Res()` 或 `@Next()`，表明你选择了特定于库的选项。如果在一个处理函数上同时使用了这两个方法，那么此处的标准方式就是自动禁用此路由, 你将不会得到你想要的结果。如果需要在某个处理函数上同时使用这两种方法（例如，通过注入响应对象，单独设置 cookie / header，但把其余部分留给框架），你必须在装饰器 `@Res({ passthrough: true })` 中将 `passthrough` 选项设为 `true`

### 状态码

默认情况下，响应的**状态码**总是默认为 **200**，除了 POST 请求（默认响应状态码为 **201**），我们可以通过在处理函数外添加 `@HttpCode（...）` 装饰器来轻松更改此行为。

```typescript
@Post()
@HttpCode(204)
create() {
  return 'This action adds a new cat';
}
```

`HttpCode` 需要从 `@nestjs/common` 包导入。

通常，状态码不是固定的，而是取决于各种因素。在这种情况下，您可以使用类库特有（library-specific）的 **`response`** （通过 ` @Res()` 注入 ）对象（或者在出现错误时，抛出异常）

### Headers

要指定自定义响应头，可以使用 `@header()` 装饰器或类库特有的响应对象，（并直接调用 `res.header()`）。

```typescript
@Post()
@Header('Cache-Control', 'none')
create() {
  return 'This action adds a new cat';
}
```

`Header` 需要从 `@nestjs/common` 包导入。

### 重定向

要将响应重定向到特定的 `URL`，可以使用 `@Redirect()` 装饰器或特定于库的响应对象（并直接调用 `res.redirect()`）。

`@Redirect()` 装饰器有两个可选参数，`url` 和 `statusCode`。 如果省略，则 `statusCode` 默认为 `302`。

```typescript
@Get()
@Redirect('https://nestjs.com', 301)
```

有时您可能想动态地决定 HTTP 状态代码或重定向 URL。通过从路由处理方法返回一个如下格式的对象：

```json
{
  "url": string,
  "statusCode": number
}
```

返回的值将覆盖传递给 `@Redirect()` 装饰器的所有参数。 例如：

```typescript
@Get('docs')
@Redirect('https://docs.nestjs.com', 302)
getDocs(@Query('version') version) {
  if (version && version === '5') {
    return { url: 'https://docs.nestjs.com/v5/' };
  }
}
```

### 类库特有方式

到目前为止，我们已经讨论了 Nest 操作响应的标准方式。操作响应的第二种方法是使用类库特有的 [响应对象(Response)](http://expressjs.com/en/api.html#res)。为了注入特定的响应对象，我们需要使用 `@Res()` 装饰器。为了对比差异，让我们来重写 `CatsController`：

```typescript
/* cats.controller.ts */

import { Controller, Get, Post, Res, HttpStatus } from '@nestjs/common';
import { Response } from 'express';

@Controller('cats')
export class CatsController {
  @Post()
  create(@Res() res: Response) {
    res.status(HttpStatus.CREATED).send();
  }

  @Get()
  findAll(@Res() res: Response) {
    res.status(HttpStatus.OK).json([]);
  }
}
```

尽管此方法有效，并且实际上通过提供对响应对象的完全控制（标头操作，特定于库的功能等）在某些方面提供了更大的灵活性，但应谨慎使用此种方法。通常来说，这种方式非常不清晰，并且有一些缺点。 主要的缺点是你的代码变得依赖于平台（因为不同的底层库在响应对象（Response）上可能具有不同的 API），并且更加难以测试（您必须模拟响应对象等）。

而且，在上面的示例中，你失去与依赖于 Nest 标准响应处理的 Nest 功能（例如，拦截器（Interceptors） 和 `@HttpCode()`/`@Header()` 装饰器）的兼容性。要解决此问题，可以将 `passthrough` 选项设置为 `true`，如下所示：

```typescript
@Get()
findAll(@Res({ passthrough: true }) res: Response) {
  res.status(HttpStatus.OK);
  return [];
}
```

现在，你就能与底层框架原生的响应对象（Response）进行交互（例如，根据特定条件设置 Cookie 或 HTTP 头），并将剩余的部分留给 Nest 处理。

## 作用域

对于来自不同编程语言背景的人来说，可能对 Nest 中几乎所有内容都可以在传入的请求之间共享感到非常意外。例如，我们有一个数据库连接池，具有全局状态的单例服务等。请记住，`Node.js` 并不遵循请求/响应多线程无状态模型（在该模型中，每个请求都由单独的线程处理），在 Nest 中，每个请求都由主线程处理。因此，使用单例实例对我们的应用程序来说是完全安全的。

但是，存在基于请求的控制器生命周期可能是期望行为的边缘情况，例如 `GraphQL` 应用程序中的请求缓存，请求跟踪或多租户。在 [这里](javascript:void(0)) 学习如何控制作用域。

## 异步性

我们酷爱现代 Javascript，并且我们知道数据读取（data extraction）大多是**异步**的.这就是为什么 Nest 完美支持**异步函数**（Async Function）特性的原因。

每个异步函数都必须返回一个 `Promise`。这意味着您可以返回延迟值，而 Nest 将自行解析它。让我们看看下面这个例子:

```typescript
/* cats.controller.ts */

@Get()
async findAll(): Promise<any[]> {
  return [];
}Copy to clipboardErrorCopied
```

这是完全有效的。此外，通过返回 RxJS [observable 流](http://reactivex.io/rxjs/class/es8/Observable.js~Observable.html)，Nest 路由处理程序将更加强大。 Nest 将自动订阅下面的源并获取最后发出的值（在流完成后）。

```typescript
/* cats.controller.ts */

@Get()
findAll(): Observable<any[]> {
  return of([]);
}Copy to clipboardErrorCopied
```

上述的两种方法都是可行的，你可以选择你喜欢的方式。

## 请求负载

此前我们列举的的 `POST` 路由处理程序样例中，处理程序没有接受任何客户端参数。我们在这里通过添加 `@Body()` 参数来解决这个问题。

首先（如果您使用 TypeScript），我们需要确定 `DTO`（数据传输对象）模式。`DTO` 是一个对象，它定义了如何通过网络发送数据。我们可以通过使用 **TypeScript** 接口（Interface）或简单的类（Class）来定义 DTO 模式。有趣的是，我们在这里推荐使用**类**。为什么？类是 JavaScript ES6 标准的一部分，因此它们在编译后的 JavaScript 中被保留为实际实体。另一方面，由于 TypeScript 接口在转换过程中被删除，所以 Nest 不能在运行时引用它们。这一点很重要，因为诸如**管道**（Pipe）之类的特性为在运行时访问变量的元类型提供更多的可能性。

现在，我们来创建 `CreateCatDto` 类：

```typescript
/*
  create-cat.dto.ts
*/
export class CreateCatDto {
  readonly name: string;
  readonly age: number;
  readonly breed: string;
}
```

它只有三个基本属性。 之后，我们可以在 `CatsController` 中使用新创建的 `DTO`：

```typescript
/* cats.controller.ts */

@Post()
async create(@Body() createCatDto: CreateCatDto) {
  return 'This action adds a new cat';
}
```

## 与 Module 绑定

控制器已经准备就绪，可以使用，但是 Nest 依然不知道 `CatsController` 是否存在，所以它不会创建这个类的一个实例。

控制器总是属于模块，这就是为什么我们在 `@Module()` 装饰器中包含 `controllers` 数组的原因。 由于除了根模块 `AppModule` 之外，我们还没有定义其他模块，所以我们将使用它来介绍 `CatsController`：

```typescript
/* app.module.ts */

import { Module } from '@nestjs/common';
import { CatsController } from './cats/cats.controller';

@Module({
  controllers: [CatsController],
})
export class AppModule {}Copy to clipboardErrorCopied
```

我们使用 `@Module()` 装饰器将元数据附加到模块类中，现在，Nest 可以轻松反射（reflect）出哪些控制器（controller）必须被安装。

# Providers

Providers 是 `Nest` 的一个基本概念。许多基本的 `Nest` 类可能被视为 provider - `service`,` repository`, `factory`, `helper` 等等。 他们都可以通过 `constructor` **注入**依赖关系。 这意味着对象可以彼此创建各种关系，并且“连接”对象实例的功能在很大程度上可以委托给 `Nest` 运行时系统。 Provider 只是一个用 `@Injectable()` 装饰器注释的类。

<img src="nest-basic/image-20210801104607853.png" alt="image-20210801104607853" style="zoom:80%;" />

在前面的章节中，我们已经创建了一个简单的控制器 `CatsController` 。

控制器应处理 `HTTP` 请求并将**更复杂的任务**委托给 **providers**。`Providers` 是纯粹的 `JavaScript` 类，在其类声明之前带有 `@Injectable()` 装饰器。

## 服务

### 基本示例

让我们从创建一个简单的 `CatsService` 开始。该服务将负责数据存储和检索，其由 `CatsController` 使用，因此把它定义为 `provider`，是一个很好的选择。因此，我们用 `@Injectable()` 来装饰这个类 。

```typescript
// cats.service.ts
import { Injectable } from '@nestjs/common';
import { Cat } from './interfaces/cat.interface';

@Injectable()
export class CatsService {
  private readonly cats: Cat[] = [];

  create(cat: Cat) {
    this.cats.push(cat);
  }

  findAll(): Cat[] {
    return this.cats;
  }
}
```

> 要使用 `CLI` 创建服务类，只需执行 `$ nest g service cats` 命令。

我们的 `CatsService` 是具有一个属性和两个方法的基本类。唯一的新特点是它使用 `@Injectable()` 装饰器。该 `@Injectable()` 附加有元数据，因此 `Nest` 知道这个类是一个 `Nest` provider。需要注意的是，上面有一个 `Cat` 接口。看起来像这样：

```typescript
// interfaces/cat.interface.ts
export interface Cat {
  name: string;
  age: number;
  breed: string;
}
```

现在我们有一个服务类来检索 `cat` ，让我们在 `CatsController` 里使用它 ：

```typescript
// cats.controller.ts
import { Controller, Get, Post, Body } from '@nestjs/common';
import { CreateCatDto } from './dto/create-cat.dto';
import { CatsService } from './cats.service';
import { Cat } from './interfaces/cat.interface';

@Controller('cats')
export class CatsController {
  constructor(private catsService: CatsService) {}

  @Post()
  async create(@Body() createCatDto: CreateCatDto) {
    this.catsService.create(createCatDto);
  }

  @Get()
  async findAll(): Promise<Cat[]> {
    return this.catsService.findAll();
  }
}
```

`CatsService` 是通过类构造函数注入的。注意这里使用了私有的只读语法。这意味着我们已经在同一位置创建并初始化了 `catsService ` 成员。

### 注册提供者

现在我们已经定义了提供者（`CatsService`），并且已经有了该服务的使用者（`CatsController`），我们需要在 `Nest` 中注册该服务，以便它可以执行注入。 为此，我们可以编辑模块文件（`app.module.ts`），然后将服务添加到 `@Module()` 装饰器的 `providers` 数组中。

```typescript
// app.module.ts
import { Module } from '@nestjs/common';
import { CatsController } from './cats/cats.controller';
import { CatsService } from './cats/cats.service';

@Module({
  controllers: [CatsController],
  providers: [CatsService],
})
export class AppModule {}Copy to clipboardErrorCopied
```

得益于此，`Nest` 现在将能够解决 `CatsController` 类的依赖关系。这就是我们目前的目录结构：

```
src
├── cats
│    ├──dto
│    │   └──create-cat.dto.ts
│    ├── interfaces
│    │       └──cat.interface.ts
│    ├──cats.service.ts
│    └──cats.controller.ts
├──app.module.ts
└──main.ts
```

## 生命周期/作用域

Provider 通常具有与应用程序生命周期同步的生命周期（“作用域”）。在启动应用程序时，必须解析每个依赖项，因此必须实例化每个提供程序。同样，当应用程序关闭时，每个 provider 都将被销毁。但是，有一些方法可以改变 provider 生命周期的请求范围。您可以 [在此处](https://docs.nestjs.cn/8/fundamentals?id=注射范围) 详细了解这些技术。

## 可选提供者

有时，您可能需要解决一些依赖项。例如，您的类可能依赖于一个**配置对象**，但如果没有传递，则应使用默认值。在这种情况下，关联变为可选的， `provider` 不会因为缺少配置导致错误。

要指示 provider 是可选的，请在 `constructor` 的参数中使用 `@Optional()` 装饰器。

```typescript
import { Injectable, Optional, Inject } from '@nestjs/common';

@Injectable()
export class HttpService<T> {
  constructor(
    @Optional() @Inject('HTTP_OPTIONS') private readonly httpClient: T
  ) {}
}
```

请注意，在上面的示例中，我们使用自定义 `provider`，这是我们包含 `HTTP_OPTIONS` 自定义标记的原因。前面的示例显示了基于构造函数的注入，通过构造函数中的类指示依赖关系。[在此处](https://docs.nestjs.cn/8/fundamentals) 详细了解自定义 providers 及其关联的 `token`。

## 基于属性的注入

我们目前使用的技术称为基于构造函数的注入，即通过构造函数方法注入 providers。在某些非常特殊的情况下，基于属性的注入可能会有用。例如，如果顶级类依赖于一个或多个 providers，那么通过从构造函数中调用子类中的 `super()` 来传递它们就会非常烦人了。因此，为了避免出现这种情况，可以在属性上使用 `@Inject()` 装饰器。

```typescript
import { Injectable, Inject } from '@nestjs/common';

@Injectable()
export class HttpService<T> {
  @Inject('HTTP_OPTIONS')
  private readonly httpClient: T;
}
```

如果您的类没有扩展其他提供者，你应该总是使用基于**构造函数**的注入。

## 手动实例化

到目前为止，我们已经讨论了 Nest 如何自动处理解决依赖关系的大多数细节。在某些情况下，您可能需要跳出内置的依赖注入系统，并手动检索或实例化提供程序。我们在下面简要讨论两个这样的主题。

要获取现有实例或动态实例化提供程序，可以使用 [Module reference](https://docs.nestjs.cn/8/fundamentals)。

要在 `bootstrap()` 函数内使用提供程序（例如，对于不带控制器的独立应用程序，或在引导过程中使用配置服务），请参见 [独立应用程序](https://docs.nestjs.cn/8/standalone-applications)。

## 自定义提供者

`Nest` 有一个内置的控制反转（`"IoC"`）容器，可以解决 providers 之间的关系。 此功能是上述依赖注入功能的基础，但要比上面描述的要强大得多。`@Injectable()` 装饰器只是冰山一角, 并不是定义 providers 的唯一方法。相反，您可以使用普通值、类、异步或同步工厂。看看 [这里](https://docs.nestjs.cn/8/fundamentals) 找到更多的例子。

# 依赖注入

Nest 是建立在强大的设计模式, 通常称为依赖注入。我们建议在官方的 [Angular文档](https://angular.cn/guide/dependency-injection) 中阅读有关此概念的精彩文章。

在 `Nest` 中，借助 **TypeScript** 功能，管理依赖项非常容易，因为它们仅按类型进行解析。在下面的示例中，`Nest` 将 `catsService` 通过创建并返回一个实例来解析 `CatsService`（或者，在单例的正常情况下，如果现有实例已在其他地方请求，则返回现有实例）。解析此依赖关系并将其传递给控制器的构造函数（或分配给指定的属性）：

```typescript
class CatsController {
		constructor(private readonly catsService: CatsService) {}
}
```

## Angular 中的依赖注入

依赖项是指某个类执行其功能所需的服务或对象。依赖项注入（DI）是一种设计模式，在这种设计模式中，类会从外部源请求依赖项而不是创建它们。

Angular 的 DI 框架会在实例化某个类时为其提供依赖。你可以使用 Angular DI 来提高应用程序的灵活性和模块化程度。

# 模块

## 概念

模块是具有 `@Module()` 装饰器的类。 `@Module()` 装饰器提供了元数据，Nest 用它来组织应用程序结构。

![image-20210801111738962](/img/user/programming/back-end/nest-basic/image-20210801111738962.png)

每个 Nest 应用程序至少有一个模块，即根模块。根模块是 Nest 开始安排应用程序树的地方。在大多数情况下，您将拥有多个模块，每个模块都有一组紧密相关的**功能**。

`@module()` 装饰器接受一个描述模块属性的对象：

| providers   | 由 Nest 注入器实例化的提供者，并且可以至少在整个模块中共享 |
| ----------- | ---------------------------------------------------------- |
| controllers | 必须创建的一组控制器                                       |
| imports     | 导入模块的列表，这些模块导出了此模块中所需提供者           |
| exports     | 由本模块提供并应在其他模块中可用的提供者的子集。           |

默认情况下，该模块**封装**提供程序。这意味着无法注入既不是当前模块的直接组成部分（providers、controllers），也不是从导入的模块导出的提供程序（imports）。因此，您可以将从模块导出的提供程序（exports）视为模块的公共接口或 API。

## 功能模块

`CatsController` 和 `CatsService` 属于同一个应用程序域。 应该考虑将它们移动到一个功能模块下，即 `CatsModule`。

```ts
// cats/cats.module.ts
import { Module } from '@nestjs/common';
import { CatsController } from './cats.controller';
import { CatsService } from './cats.service';

@Module({
  controllers: [CatsController],
  providers: [CatsService],
})
export class CatsModule {}
```

> 要使用 CLI 创建模块，只需执行 `$ nest g module cats` 命令

我已经创建了 `cats.module.ts` 文件，并把与这个模块相关的所有东西都移到了 cats 目录下。我们需要做的最后一件事是将这个模块导入根模块 `(ApplicationModule)`。

```ts
import { Module } from '@nestjs/common';
import { CatsModule } from './cats/cats.module';

@Module({
  imports: [CatsModule],
})
export class ApplicationModule {}
```

现在 `Nest` 知道除了 `ApplicationModule` 之外，注册 `CatsModule` 也是非常重要的。 这就是我们现在的目录结构:

```
rc
├──cats
│    ├──dto
│    │   └──create-cat.dto.ts
│    ├──interfaces
│    │     └──cat.interface.ts
│    ├─cats.service.ts
│    ├─cats.controller.ts
│    └──cats.module.ts
├──app.module.ts
└──main.ts
```

## 共享模块

在 Nest 中，默认情况下，模块是**单例**，因此您可以轻松地在多个模块之间共享**同一个**提供者实例

![image-20210801112532851](/img/user/programming/back-end/nest-basic/image-20210801112532851.png)

实际上，每个模块都是一个**共享模块**。一旦创建就能被任意模块重复使用。假设我们将在几个模块之间共享 `CatsService` 实例。 我们需要把 `CatsService` 放到 `exports` 数组中，如下所示：

```ts
// cats.module.ts
import { Module } from '@nestjs/common';
import { CatsController } from './cats.controller';
import { CatsService } from './cats.service';

@Module({
  controllers: [CatsController],
  providers: [CatsService],
  exports: [CatsService]
})
export class CatsModule {}
```

现在，每个导入 `CatsModule` 的模块都可以访问 `CatsService` ，并且它们将共享相同的 `CatsService` 实例。

## 模块导出

模块可以导出他们的内部提供者。 而且，他们可以再导出自己导入的模块。

```typescript
@Module({
  imports: [CommonModule],
  exports: [CommonModule],
})
export class CoreModule {}
```

## 注入提供者

提供者也可以注入到模块 (类) 中（例如，用于配置目的）：

```typescript
// cats.module.ts
import { Module } from '@nestjs/common';
import { CatsController } from './cats.controller';
import { CatsService } from './cats.service';

@Module({
  controllers: [CatsController],
  providers: [CatsService],
})
export class CatsModule {
  constructor(private readonly catsService: CatsService) {}
}
```

但是，由于 [循环依赖](https://docs.nestjs.cn/8/fundamentals?id=circular-dependency) 性，模块类不能注入到提供者中。

## 全局模块

如果你不得不在任何地方导入相同的模块，那可能很烦人。在 [Angular](https://angular.io/) 中，提供者是在全局范围内注册的。一旦定义，他们到处可用。另一方面，Nest 将提供者封装在模块范围内。您无法在其他地方使用模块的提供者而不导入他们。但是有时候，你可能只想提供一组随时可用的东西 - 例如：helper，数据库连接等等。这就是为什么你能够使模块成为全局模块。

```ts
import { Module, Global } from '@nestjs/common';
import { CatsController } from './cats.controller';
import { CatsService } from './cats.service';

@Global()
@Module({
  controllers: [CatsController],
  providers: [CatsService],
  exports: [CatsService],
})
export class CatsModule {}
```

`@Global` 装饰器使模块成为全局作用域。 全局模块应该只注册一次，最好由根或核心模块注册。 在上面的例子中，`CatsService` 组件将无处不在，而想要使用 `CatsService` 的模块则不需要在 `imports` 数组中导入 `CatsModule`。

>  使一切全局化并不是一个好的解决方案。 全局模块可用于减少必要模板文件的数量。 `imports` 数组仍然是使模块 API 透明的最佳方式。

## 动态模块

`Nest` 模块系统包括一个称为动态模块的强大功能。此功能使您可以轻松创建可自定义的模块，这些模块可以动态注册和配置提供程序。动态模块在这里广泛介绍。在 [本章](https://docs.nestjs.cn/8/fundamentals/dynamic-modules) 中，我们将简要概述以完成模块介绍。

以下是一个动态模块定义的示例 `DatabaseModule`：

```typescript
import { Module, DynamicModule } from '@nestjs/common';
import { createDatabaseProviders } from './database.providers';
import { Connection } from './connection.provider';

@Module({
  providers: [Connection],
})
export class DatabaseModule {
  static forRoot(entities = [], options?): DynamicModule {
    const providers = createDatabaseProviders(options, entities);
    return {
      module: DatabaseModule,
      providers: providers,
      exports: providers,
    };
  }
}
```

`forRoot()` 可以同步或异步（`Promise`）返回动态模块。

此模块 `Connection` 默认情况下（在 `@Module()` 装饰器元数据中）定义提供程序，但此外 - 根据传递给方法的 `entities` 和 `options` 对象 `forRoot()` - 公开提供程序的集合，例如存储库。请注意，动态模块返回的属性扩展（而不是覆盖）`@Module()` 装饰器中定义的基本模块元数据。这就是从模块导出静态声明的 `Connection` 提供程序和动态生成的存储库提供程序的方式。

如果要在全局范围内注册动态模块，请将 `global` 属性设置为 `true`。

```typescript
{
  global: true,
  module: DatabaseModule,
  providers: providers,
  exports: providers,
}
```

如上所述，将所有内容全局化不是一个好的设计决策。

所述 `DatabaseModule` 可以被导入，并且被配置以下列方式：

```typescript
import { Module } from '@nestjs/common';
import { DatabaseModule } from './database/database.module';
import { User } from './users/entities/user.entity';

@Module({
  imports: [DatabaseModule.forRoot([User])],
})
export class AppModule {}
```

如果要依次重新导出动态模块，则可以 `forRoot()` 在导出数组中省略方法调用：

```typescript
import { Module } from '@nestjs/common';
import { DatabaseModule } from './database/database.module';
import { User } from './users/entities/user.entity';

@Module({
  imports: [DatabaseModule.forRoot([User])],
  exports: [DatabaseModule],
})
export class AppModule {}
```

[动态模块](https://docs.nestjs.cn/8/fundamentals/dynamic-modules) 章介绍中更详细地在本主题，并且包括一个 [实例](https://github.com/nestjs/nest/tree/master/sample/25-dynamic-modules)。

# 中间件

## 概念

中间件是在路由处理程序 **之前** 调用的函数。 中间件函数可以访问请求和响应对象，以及应用程序请求响应周期中的 `next()` 中间件函数。 `next()` 中间件函数通常由名为 `next` 的变量表示。

![image-20210801150359798](/img/user/programming/back-end/nest-basic/image-20210801150359798.png)

Nest 中间件实际上等价于 [express](http://expressjs.com/en/guide/using-middleware.html) 中间件。 下面是 Express 官方文档中所述的中间件功能：

中间件函数可以执行以下任务:

- 执行任何代码。
- 对请求和响应对象进行更改。
- 结束请求 - 响应周期。
- 调用堆栈中的下一个中间件函数。
- 如果当前的中间件函数没有结束请求 - 响应周期, 它必须调用 `next()` 将控制传递给下一个中间件函数。否则, 请求将被挂起。

您可以在函数中或在具有 `@Injectable()` 装饰器的类中实现自定义 `Nest` 中间件。 这个类应该实现 `NestMiddleware` 接口, 而函数没有任何特殊的要求。 让我们首先使用类方法实现一个简单的中间件功能。

```ts
// logger
import { Injectable, NestMiddleware } from '@nestjs/common';
import { Request, Response, NextFunction } from 'express';

@Injectable()
export class LoggerMiddleware implements NestMiddleware {
  use(req: Request, res: Response, next: NextFunction) {
    console.log('Request...');
    next();
  }
}
```

### 使用场景

统一校验路由参数，如果路由参数是不符合预期的可以直接返回 error

## 依赖注入

`Nest` 中间件完全支持依赖注入。 就像提供者和控制器一样，它们能够**注入**属于同一模块的依赖项（通过 `constructor` ）

## 应用中间件

中间件不能在 `@Module()` 装饰器中列出。我们必须使用模块类的 `configure()` 方法来设置它们。包含中间件的模块必须实现 `NestModule` 接口。我们将 `LoggerMiddleware` 设置在 `ApplicationModule` 层上。

### 中间件消费者

`MiddlewareConsumer` 是一个帮助类。它提供了几种内置方法来管理中间件。他们都可以被简单地**链接**起来。`forRoutes()` 可接受一个字符串、多个字符串、对象、一个控制器类甚至多个控制器类。在大多数情况下，您可能只会传递一个由逗号分隔的控制器列表。以下是单个控制器的示例：

```ts
consumer
  .apply(LoggerMiddleware)
  .exclude(
    { path: 'cats', method: RequestMethod.GET },
    { path: 'cats', method: RequestMethod.POST },
    'cats/(.*)',
  )
  .forRoutes(CatsController);
```

我们还可以在配置中间件时将包含路由路径的对象和请求方法传递给 `forRoutes()` 方法。

```ts
// app.module.ts
import { Module, NestModule, MiddlewareConsumer } from '@nestjs/common';
import { LoggerMiddleware } from './common/middleware/logger.middleware';
import { CatsModule } from './cats/cats.module';

@Module({
  imports: [CatsModule],
})
export class AppModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    consumer
      .apply(LoggerMiddleware)
      .forRoutes('cats');
  }
}
```

我们为之前在 `CatsController` 中定义的 `/cats` 路由处理程序设置了 `LoggerMiddleware`。我们还可以在配置中间件时将包含路由路径的对象和请求方法传递给 `forRoutes()` 方法，从而进一步将中间件限制为特定的请求方法。在下面的示例中，请注意我们导入了 `RequestMethod` 来引用所需的请求方法类型。

```ts
// app.module.ts
import { Module, NestModule, RequestMethod, MiddlewareConsumer } from '@nestjs/common';
import { LoggerMiddleware } from './common/middleware/logger.middleware';
import { CatsModule } from './cats/cats.module';

@Module({
  imports: [CatsModule],
})
export class AppModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    consumer
      .apply(LoggerMiddleware)
      .forRoutes({ path: 'cats', method: RequestMethod.GET });
  }
}
```

### 路由通配符 @link

在此处同样适用

### 排除路由

有时我们想从应用中间件中排除某些路由。我们可以使用该 `exclude()` 方法轻松排除某些路由。此方法可以采用一个字符串，多个字符串或一个 `RouteInfo` 对象来标识要排除的路由，如下所示：

```ts
consumer
  .apply(LoggerMiddleware)
  .exclude(
    { path: 'cats', method: RequestMethod.GET },
    { path: 'cats', method: RequestMethod.POST },
    'cats/(.*)',
  )
  .forRoutes(CatsController);
```

### 多个中间件

如前所述，为了绑定顺序执行的多个中间件，我们可以在 `apply()` 方法内用逗号分隔它们。

```ts
consumer
  	.apply(cors(), helmet(), logger)
  	.forRoutes(CatsController);
```

### 全局中间件

如果我们想一次性将中间件绑定到**每个注册路由**，我们可以使用由 `INestApplication` 实例提供的 `use()` 方法：

```ts
const app = await NestFactory.create(AppModule);
app.use(logger);
await app.listen(3000);
```

### 函数式中间件

我们使用的 `LoggerMiddleware` 类非常简单。它没有成员，没有额外的方法，没有依赖关系。为什么我们不能只使用一个简单的函数？这是一个很好的问题，因为事实上 - 我们可以做到。这种类型的中间件称为**函数式中间件**。让我们把 `logger` 转换成函数。

```ts
export function logger(req, res, next) {
  console.log(`Request...`);
  next();
};
```

现在在 `AppModule` 中使用它。

```ts
consumer
  .apply(logger)
  .forRoutes(CatsController);
```

当您的中间件没有任何依赖关系时，我们可以考虑使用函数式中间件。

# 异常处理

内置的**异常层**负责处理整个应用程序中的所有抛出的异常。当捕获到未处理的异常时，最终用户将收到友好的响应。

![image-20210801152238621](/img/user/programming/back-end/nest-basic/image-20210801152238621.png)

开箱即用，此操作由内置的全局异常过滤器执行，该过滤器处理类型 `HttpException`（及其子类）的异常。每个发生的异常都由全局异常过滤器处理, 当这个异常**无法被识别**时 (既不是 `HttpException` 也不是继承的类 `HttpException` ) , 用户将收到以下 `JSON` 响应:

```ts
{
    "statusCode": 500,
    "message": "Internal server error"
}
```

## 基本原则

遇到错误直接 throw，会停止 js 线程的执行，后续的逻辑没有必要每次都判断是否存在了

## 基础异常类

`Nest` 提供了一个内置的 `HttpException` 类，它从 `@nestjs/common` 包中导入。对于典型的基于 `HTTP` `REST/GraphQL` `API` 的应用程序，最佳实践是在发生某些错误情况时发送标准 HTTP 响应对象。

在 `CatsController`，我们有一个 `findAll()` 方法（`GET` 路由）。假设此路由处理程序由于某种原因引发异常。 为了说明这一点，我们将对其进行如下硬编码：

```ts
@Get()
async findAll() {
  throw new HttpException('Forbidden', HttpStatus.FORBIDDEN);
}
```

> 我们在这里使用了 `HttpStatus` 。它是从 `@nestjs/common` 包导入的辅助枚举器。

现在当客户端调用这个端点时，响应如下所示：

```ts
{
    "statusCode": 403,
    "message": "Forbidden"
}
```

`HttpException` 构造函数有两个必要的参数来决定响应:

- `response` 参数定义 `JSON` 响应体。它可以是 `string` 或 `object`，如下所述。
- `status` 参数定义 `HTTP`[状态代码](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)。

默认情况下，`JSON` 响应主体包含两个属性：

- `statusCode`：默认为 `status` 参数中提供的 `HTTP` 状态代码
- `message`: 基于状态的 `HTTP` 错误的简短描述

仅覆盖 `JSON` 响应主体的消息部分，请在 `response` 参数中提供一个 `string`。

要覆盖整个 `JSON` 响应主体，请在 `response` 参数中传递一个 `object`。 `Nest` 将序列化对象，并将其作为 `JSON` 响应返回。

第二个构造函数参数 -`status`- 是有效的 `HTTP` 状态代码。 最佳实践是使用从 `@nestjs/common` 导入的 `HttpStatus` 枚举。

这是一个覆盖整个响应正文的示例：

```ts
@Get()
async findAll() {
  throw new HttpException({
    status: HttpStatus.FORBIDDEN,
    error: 'This is a custom message',
  }, HttpStatus.FORBIDDEN);
}
```

## 内置 HTTP 异常

为了减少样板代码，Nest 提供了一系列继承自核心异常 `HttpException` 的可用异常。所有这些都可以在 `@nestjs/common` 包中找到：

- `BadRequestException`
- `UnauthorizedException`
- `NotFoundException`
- `ForbiddenException`
- `NotAcceptableException`
- `RequestTimeoutException`
- `ConflictException`
- `GoneException`
- `PayloadTooLargeException`
- `UnsupportedMediaTypeException`
- `UnprocessableException`
- `InternalServerErrorException`
- `NotImplementedException`
- `BadGatewayException`
- `ServiceUnavailableException`
- `GatewayTimeoutException`

## 自定义异常

在许多情况下，您无需编写自定义异常，而可以使用内置的 `Nest HTTP` 异常， 如果确实需要创建自定义的异常，则最好创建自己的**异常层次结构**，其中自定义异常从基 `HttpException` 类继承。 使用这种方法，`Nest` 可以识别您的异常，并自动处理错误响应。 让我们实现这样一个自定义异常：

```ts
// forbidden.exception.ts
export class ForbiddenException extends HttpException {
  constructor() {
    super('Forbidden', HttpStatus.FORBIDDEN);
  }
}
```

由于 `ForbiddenException` 扩展了基础 `HttpException`，它将和核心异常处理程序一起工作，因此我们可以在 `findAll()` 方法中使用它。

## 异常过滤器

虽然基本（内置）异常过滤器可以为您自动处理许多情况，但有时您可能希望对异常层拥有**完全控制权**，例如，您可能要添加日志记录或基于一些动态因素使用其他 `JSON` 模式。 **异常过滤器**正是为此目的而设计的。 它们使您可以控制精确的控制流以及将响应的内容发送回客户端。

让我们创建一个异常过滤器，它负责捕获作为 `HttpException` 类实例的异常，并为它们设置自定义响应逻辑。为此，我们需要访问底层平台 `Request` 和 `Response`。我们将访问 `Request` 对象，以便提取原始 `url` 并将其包含在日志信息中。我们将使用 `Response.json()` 方法，使用 `Response` 对象直接控制发送的响应。

```ts
// http-exception.filter.ts
import { ExceptionFilter, Catch, ArgumentsHost, HttpException } from '@nestjs/common';
import { Request, Response } from 'express';

@Catch(HttpException)
export class HttpExceptionFilter implements ExceptionFilter {
  catch(exception: HttpException, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse<Response>();
    const request = ctx.getRequest<Request>();
    const status = exception.getStatus();

    response
      .status(status)
      .json({
        statusCode: status,
        timestamp: new Date().toISOString(),
        path: request.url,
      });
  }
}
```

> 所有异常过滤器都应该实现通用的 `ExceptionFilter<T>` 接口。它需要你使用有效签名提供 `catch(exception: T, host: ArgumentsHost)` 方法。`T` 表示异常的类型。

`@Catch()` 装饰器绑定所需的元数据到异常过滤器上。它告诉 `Nest` 这个特定的过滤器正在寻找 `HttpException` 而不是其他的。在实践中，`@Catch()` 可以传递多个参数，所以你可以通过逗号分隔来为多个类型的异常设置过滤器。

## ArgumentsHost

让我们看一下该 `catch()` 方法的参数。该 `exception` 参数是当前正在处理的异常对象。该 host 参数是一个 `ArgumentsHost` 对象。 `ArgumentsHost` 是一个功能强大的实用程序对象，我们将在执行上下文章节 * 中进一步进行研究。在此代码示例中，我们使用它来获取对 `Request` 和 `Response` 对象的引用，这些对象被传递给原始请求处理程序（在异常发生的控制器中）。在此代码示例中，我们使用了一些辅助方法 `ArgumentsHost` 来获取所需的 `Request` 和 `Response` 对象。`ArgumentsHost` 在此处了解更多信息。

之所以如此抽象，是因为它 `ArgumentsHost` 可以在所有上下文中使用（例如，我们现在正在使用的 `HTTP` 服务器上下文，以及微服务和 `WebSocket` ）。在执行上下文章中，我们会看到我们如何可以访问相应的基础参数进行任何与动力执行上下文 `ArgumentsHost` 和它的辅助功能。这将使我们能够编写可在所有上下文中运行的通用异常过滤器。

## 绑定过滤器

让我们将 `HttpExceptionFilter` 绑定到 `CatsController` 的 `create()` 方法上。

```ts
@Post()
@UseFilters(new HttpExceptionFilter())
async create(@Body() createCatDto: CreateCatDto) {
  throw new ForbiddenException();
}
```

> `@UseFilters()` 装饰器需要从 `@nestjs/common` 包导入。

我们在这里使用了 `@UseFilters()` 装饰器。和 `@Catch()` 装饰器类似，它可以使用单个过滤器实例，也可以使用逗号分隔的过滤器实例列表。 我们创建了 `HttpExceptionFilter` 的实例。另一种可用的方式是传递类（不是实例），让框架承担实例化责任并启用依赖注入。

```ts
@Post()
@UseFilters(HttpExceptionFilter)
async create(@Body() createCatDto: CreateCatDto) {
  throw new ForbiddenException();
}
```

尽可能使用类而不是实例。由于 `Nest` 可以轻松地在整个模块中重复使用同一类的实例，因此可以减少**内存使用**。

在上面的示例中，`HttpExceptionFilter` 仅应用于单个 `create()` 路由处理程序，使其成为方法范围的。 异常过滤器的作用域可以划分为不同的级别：方法范围，控制器范围或全局范围。 例如，要将过滤器设置为控制器作用域，您可以执行以下操作：

```ts
@UseFilters(new HttpExceptionFilter())
export class CatsController {}
```

此结构为 `CatsController` 中的每个路由处理程序设置 `HttpExceptionFilter`。

### 全局过滤器

要创建一个全局范围的过滤器，您需要执行以下操作:

```ts
async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.useGlobalFilters(new HttpExceptionFilter());
  await app.listen(3000);
}
bootstrap();
```

该 `useGlobalFilters()` 方法不会为网关和混合应用程序设置过滤器。

全局过滤器用于整个应用程序、每个控制器和每个路由处理程序。就依赖注入而言，从任何模块外部注册的全局过滤器（使用上面示例中的 `useGlobalFilters()`）不能注入依赖，因为它们不属于任何模块。为了解决这个问题，你可以注册一个全局范围的过滤器直接为任何模块设置过滤器：

```ts
import { Module } from '@nestjs/common';
import { APP_FILTER } from '@nestjs/core';

@Module({
  providers: [
    {
      provide: APP_FILTER,
      useClass: HttpExceptionFilter,
    },
  ],
})
export class AppModule {}
```

当使用此方法对过滤器执行依赖注入时，请注意，无论采用哪种结构的模块，过滤器实际上都是全局的。 应该在哪里做？ 选择定义了过滤器（以上示例中为 `HttpExceptionFilter`）的模块。 同样，`useClass` 不是处理自定义提供程序注册的唯一方法。 也可以使用自定义处理器，在 [这里](https://docs.nestjs.cn/8/fundamentals/custom-providers) 了解更多。

您可以根据需要添加任意数量的过滤器; 只需将每个组件添加到 `providers`（提供者）数组。

## 捕获异常

为了捕获每一个未处理的异常 (不管异常类型如何)，将 `@Catch()` 装饰器的参数列表设为空，例如 `@Catch()`。

```ts
// any-exception.filter.ts
import {
  ExceptionFilter,
  Catch,
  ArgumentsHost,
  HttpException,
  HttpStatus,
} from '@nestjs/common';

@Catch()
export class AllExceptionsFilter implements ExceptionFilter {
  catch(exception: unknown, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse();
    const request = ctx.getRequest();

    const status =
      exception instanceof HttpException
        ? exception.getStatus()
        : HttpStatus.INTERNAL_SERVER_ERROR;

    response.status(status).json({
      statusCode: status,
      timestamp: new Date().toISOString(),
      path: request.url,
    });
  }
}
```

## 继承

通常，您将创建完全定制的异常过滤器，以满足您的应用程序需求。如果您希望重用已经实现的核心异常过滤器，并基于某些因素重写行为，请看下面的例子。

为了将异常处理委托给基础过滤器，需要继承 `BaseExceptionFilter` 并调用继承的 `catch()` 方法。

> all-exceptions.filter.ts

```typescript
import { Catch, ArgumentsHost } from '@nestjs/common';
import { BaseExceptionFilter } from '@nestjs/core';

@Catch()
export class AllExceptionsFilter extends BaseExceptionFilter {
  catch(exception: unknown, host: ArgumentsHost) {
    super.catch(exception, host);
  }
}
```

继承自基础类的过滤器必须由框架本身实例化（不要使用 `new` 关键字手动创建实例）

上面的实现只是一个演示。扩展异常过滤器的实现将包括定制的业务逻辑 (例如，处理各种情况)。

全局过滤器可以扩展基本过滤器。这可以通过两种方式来实现。

您可以通过注入 `HttpServer` 来使用继承自基础类的全局过滤器。

```typescript
async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  const { httpAdapter } = app.get(HttpAdapterHost);
  app.useGlobalFilters(new AllExceptionsFilter(httpAdapter));

  await app.listen(3000);
}
```

第二种方法是使用 `APP_FILTER` `token`，参见上例

# 管道

管道是具有 `@Injectable()` 装饰器的类。管道应实现 `PipeTransform` 接口。

![image-20210801152238621](/img/user/programming/back-end/nest-basic/image-20210801152238621.png)

管道有两个类型:

- **转换**：管道将输入数据转换为所需的数据输出
- **验证**：对输入数据进行验证，如果验证成功继续传递; 验证失败则抛出异常;

在这两种情况下, 管道 `参数(arguments)` 会由 [控制器(controllers)的路由处理程序](https://docs.nestjs.cn/8/controllers?id=路由参数) 进行处理. Nest 会在调用这个方法之前插入一个管道，管道会先拦截方法的调用参数,进行转换或是验证处理，然后用转换好或是验证好的参数调用原方法。

管道在异常区域内运行。这意味着当抛出异常时，它们由核心异常处理程序和应用于当前上下文的 [异常过滤器](https://docs.nestjs.cn/8/exceptionfilters) 处理。当在 Pipe 中发生异常，controller 不会继续执行任何方法。

## ValidationPipe

验证是任何现有 `Web` 应用程序的基本功能。为了自动验证传入请求，`Nest` 提供了一个内置的 `ValidationPipe` ，它使用了功能强大的 [class-validator](https://github.com/typestack/class-validator) 包及其声明性验证装饰器。 `ValidationPipe` 提供了一种对所有传入的客户端有效负载强制执行验证规则的便捷方法，其中在每个模块的本地类/ `DTO` 声明中使用简单的注释声明特定的规则。

### [使用内置的`ValidationPipe`](https://docs.nestjs.cn/8/techniques?id=使用内置的validationpipe)

由于此管道使用了 `class-validator` 和 `class-transformer` 库，因此有许多可用的选项。通过传递给管道的配置对象来进行配置。依照下列内置的选项：

```ts
export interface ValidationPipeOptions extends ValidatorOptions {
  transform?: boolean;
  disableErrorMessages?: boolean;
  exceptionFactory?: (errors: ValidationError[]) => any;
}
```

所有可用的 `class-validator` 选项（继承自 `ValidatorOptions` 接口）：

| 选项                   | 类型     | 描述                                                         |
| :--------------------- | :------- | :----------------------------------------------------------- |
| skipMissingProperties  | boolean  | 如果设置为 `true`，验证将跳过对所有验证对象中没有的属性的验证 |
| whitelist              | boolean  | 如果设置为 `true`，验证器将去掉没有使用任何验证装饰器的属性的验证（返回的）对象 |
| forbidNonWhitelisted   | boolean  | 如果设置为 `true`，验证器不会去掉非白名单的属性，而是会抛出异常 |
| forbidUnknownValues    | boolean  | 如果设置为 `true`，尝试验证未知对象会立即失败                 |
| disableErrorMessage    | boolean  | 如果设置为 `true`,验证错误不会返回给客户端                    |
| errorHttpStatusCode    | number   | 这个设置允许你确定在错误时使用哪个异常类型。默认抛出 `BadRequestException` |
| exceptionFactory       | Function | 接受一个验证错误数组并返回一个要抛出的异常对象               |
| groups                 | string[] | 验证对象时使用的分组                                         |
| dismissDefaultMessages | boolean  | 如果设置为 `true`，将不会使用默认消息验证，如果不设置，错误消息会始终是 `undefined` |
| validationError.target | boolean  | 确定目标是否要在 `ValidationError` 中暴露出来                  |
| validationError.value  | boolean  | 确定验证值是否要在 `ValidationError` 中暴露出来                |

更多关于 `class-validator` 包的内容见项目 [仓库](https://github.com/typestack/class-validator)。

### [自动验证](https://docs.nestjs.cn/8/techniques?id=自动验证)

我们将绑定 `ValidationPipe` 到整个应用程序，因此，将自动保护所有接口免受不正确的数据的影响。

```ts
async function bootstrap() {
  const app = await NestFactory.create(ApplicationModule);
  app.useGlobalPipes(new ValidationPipe());
  await app.listen(3000);
}
bootstrap();
```

由于 `Typescript` 没有保存 `泛型或接口` 的元数据。当你在你的 DTO 中使用他们的时候。`ValidationPipe` 可能不能正确验证输入数据。出于这种原因，可以考虑在你的 DTO 中使用具体的类。

**示例**

```ts
import { IsEmail, IsNotEmpty } from 'class-validator';

export class CreateUserDto {
  @IsEmail()
  email: string;

  @IsNotEmpty()
  password: string;
}
```

现在我们可以在 `CreateUserDto` 中添加一些验证规则。我们使用 `class-validator` 包提供的装饰器来实现这一点，[这里](https://github.com/typestack/class-validator#validation-decorators) 有详细的描述。以这种方式，任何使用 `CreateUserDto` 的路由都将自动执行这些验证规则。

有了这些规则，当某人使用无效 email 执行对我们的接口的请求时，则应用程序将自动以 `400 Bad Request` 代码以及以下响应正文进行响应：

```json
{
  "statusCode": 400,
  "error": "Bad Request",
  "message": ["email must be an email"]
}
```

除了验证请求主体之外，`ValidationPipe` 还可以与其他请求对象属性一起使用。假设我们希望接受端点路径中的 `id` 。为了确保此请求参数只接受数字，我们可以使用以下结构:

```ts
@Get(':id')
findOne(@Param() params: FindOneParams) {
  return 'This action returns a user';
}
```

与 `DTO` 一样，`FindOneParams` 只是一个使用 `class-validator` 定义验证规则的类。它是这样的:

```ts
import { IsNumberString } from 'class-validator';

export class FindOneParams {
  @IsNumberString()
  id: number;
}
```

### [禁用详细错误](https://docs.nestjs.cn/8/techniques?id=禁用详细错误)

错误消息有助于解释请求中的错误。然而，一些生产环境倾向于禁用详细的错误。通过向 `ValidationPipe` 传递一个 `options` 对象来做到这一点:

```ts
app.useGlobalPipes(
  new ValidationPipe({
    disableErrorMessages: true,
  })
);
```

### [剥离属性](https://docs.nestjs.cn/8/techniques?id=剥离属性)

我们的 `ValidationPipe` 还可以过滤掉方法处理程序不应该接收的属性。在这种情况下，我们可以对可接受的属性进行白名单，白名单中不包含的任何属性都会自动从结果对象中删除。例如，如果我们的处理程序需要 `email` 和 `password`，但是一个请求还包含一个 `age` 属性，那么这个属性可以从结果 `DTO` 中自动删除。要启用这种行为，请将白名单设置为 `true` 。

```ts
app.useGlobalPipes(
  new ValidationPipe({
    whitelist: true,
  })
);
```

当设置为 `true` 时，这将自动删除非白名单属性 (在验证类中没有任何修饰符的属性)。

或者，您可以在出现非白名单属性时停止处理请求，并向用户返回错误响应。要启用此选项，请将 `forbidNonWhitelisted` 选项属性设置为 `true` ，并将白名单设置为 `true`。

### [负载对象转换(Transform)](https://docs.nestjs.cn/8/techniques?id=负载对象转换transform)

来自网络的有效负载是普通的 `JavaScript` 对象。`ValidationPipe` 可以根据对象的 `DTO` 类自动将有效负载转换为对象类型。若要启用自动转换，请将 `transform` 设置为 `true`。

要使能自动转换选项，`ValidationPipe` 将执行简单类型转换。在下述示例中，`findOne()` 方法调用一个从地址参数中解析出的 `id` 参数。

```ts
@Get(':id')
findOne(@Param('id') id: number) {
  console.log(typeof id === 'number'); // true
  return 'This action returns a user';
}
```

默认地，每个地址参数和查询参数在网络传输时都是 `string` 类型。在上述示例中，我们指定 `id` 参数为 `number`（在方法签名中）。因此，`ValidationPipe` 会自动将 `string` 类型转换为 `number`。

### [Websockets 和 微服务](https://docs.nestjs.cn/8/techniques?id=websockets-和-微服务)

尽管本章展示了使用 `HTTP` 风格的应用程序的例子 (例如，`Express` 或 `Fastify` )， `ValidationPipe` 对于 `WebSockets` 和微服务是一样的，不管使用什么传输方法。

### [学到更多](https://docs.nestjs.cn/8/techniques?id=学到更多)

要阅读有关自定义验证器，错误消息和可用装饰器的更多信息，请访问 [此页面](https://github.com/typestack/class-validator)。

## 绑定管道

To use a pipe, we need to bind an instance of the pipe class to the appropriate context.

为了使用管道，我们需要把管道绑定到具体的上下文中

### 绑定到具体参数

在 `ParseIntPipe` 的例子中，我们通过把管道绑定在 controller 的参数上，确保其在路由处理函数执行前调用

```typescript
@Get(':id')
async findOne(@Param('id', ParseIntPipe) id: number) {
  return this.catsService.findOne(id);
}
```

一旦使用了管道，就可以确保只有以下两种情况：

1. 要么是返回一个 `number`，确保 `this.catsService.findOne()` 可以正确执行
2. 要么是在路由处理函数执行前抛出一个 `error`

### 绑定到 Controller

我们使用 `@UsePipes()` 装饰器并创建一个管道实例，并将其传递给 Joi 验证。

```typescript
@Post()
// 此处的schema不知道要怎么生成，莫名其妙的
@UsePipes(new JoiValidationPipe(createCatSchema))
async create(@Body() createCatDto: CreateCatDto) {
  this.catsService.create(createCatDto);
}
```

### 绑定到全局

```ts
app.useGlobalPipes(new ValidationPipe({ transform: true }));
```

## 内置管道

`Nest` 自带的开箱即用的管道，如下

- `ValidationPipe`
- `ParseIntPipe`
- `ParseBoolPipe`
- `ParseArrayPipe`
- `ParseUUIDPipe`
- `DefaultValuePipe`
- `ParseEnumPipe`
- `ParseFloatPipe`

他们从 `@nestjs/common` 包中导出。

### [显式转换](https://docs.nestjs.cn/8/techniques?id=显式转换)

在上述部分，我们演示了 `ValidationPipe` 如何基于期待类型隐式转换查询和路径参数，然而，这一特性需要开启自动转换功能。

可选地（在不开启自动转换功能的情况下），你可以使用 `ParseIntPipe` 或者 `ParseBoolPipe` 显式处理值（注意，没有必要使用 `ParseStringPipe`，这是因为如前所述的，网络中传输的路径参数和查询参数默认都是 `string` 类型）。

```ts
@Get(':id')
findOne(
  @Param('id', ParseIntPipe) id: number,
  @Query('sort', ParseBoolPipe) sort: boolean,
) {
  console.log(typeof id === 'number'); // true
  console.log(typeof sort === 'boolean'); // true
  return 'This action returns a user';
}
```

### [转换和验证数组](https://docs.nestjs.cn/8/techniques?id=转换和验证数组)

`TypeScript` 不存储泛型或接口的元数据，因此当你在 DTO 中使用它们的时候，`ValidationPipe` 可能不能正确验证输入数据。例如，在下列代码中，`createUserDto` 不能正确验证。

```ts
@Post()
createBulk(@Body() createUserDtos: CreateUserDto[]) {
  return 'This action adds new users';
}
```

要验证数组，创建一个包裹了该数组的专用类，或者使用 `ParseArrayPipe`。

```ts
@Post()
createBulk(
  @Body(new ParseArrayPipe({ items: CreateUserDto }))
  createUserDtos: CreateUserDto[],
) {
  return 'This action adds new users';
}
```

此外，`ParseArrayPipe` 可能需要手动解析查询参数。

假如我们有一个 `findByIds()` 方法：该方法返回作为查询参数传递的标识的 `users`

```ts
@Get()
findByIds(
  @Query('id', new ParseArrayPipe({ items: Number, separator: ',' }))
  ids: number[],
) {
  return 'This action returns users by ids';
}
```

这个构造用于验证一个来自如下形式带参数的 `GET` 请求：

```
GET /?ids=1,2,3
```

### DefaultValuePipe

`Parse*` pipes expect a parameter's value to be defined. They throw an exception upon receiving `null` or `undefined` values. To allow an endpoint to handle missing querystring parameter values, we have to provide a default value to be injected before the `Parse*` pipes operate on these values. The `DefaultValuePipe` serves that purpose. Simply instantiate a `DefaultValuePipe` in the `@Query()` decorator before the relevant `Parse*` pipe, as shown below:

```ts
@Get()
async findAll(
  @Query('activeOnly', new DefaultValuePipe(false), ParseBoolPipe) activeOnly: boolean,
  @Query('page', new DefaultValuePipe(0), ParseIntPipe) page: number,
) {
  return this.catsService.findAll({ activeOnly, page });
}
```

### 自定义内置管道的行为

上面的例子中，我们传递了一个 `class` 而不是一个实例，实际上框架会自动帮我们实例化，并完成依赖注入。通过管道和守卫，我们也可以传递一个适当的实例，我在我们需要通过 `options` 参数自定义内置管道的行为时非常有用

```ts
@Get(':id')
async findOne(
  @Param('id', new ParseIntPipe({ errorHttpStatusCode: HttpStatus.NOT_ACCEPTABLE }))
  id: number,
) {
  return this.catsService.findOne(id);
}
```

## [对象结构验证](https://docs.nestjs.cn/8/pipes?id=对象结构验证)

## 类验证器

为了更好地理解内置管道是如何工作的，我们将从头开始构建它们。

我们从 `ValidationPipe`. 开始。 首先它只接受一个值并立即返回相同的值，其行为类似于一个标识函数。

```ts
// validate.pipe.ts
import { PipeTransform, Injectable, ArgumentMetadata } from '@nestjs/common';

@Injectable()
export class ValidationPipe implements PipeTransform {
  transform(value: any, metadata: ArgumentMetadata) {
    return value;
  }
}
```

>  `PipeTransform<T, R>` 是一个通用接口，其中 `T` 表示 `value` 的类型，`R` 表示 `transform()` 方法的返回类型。

每个管道必须提供 `transform()` 方法。 这个方法有两个参数：

- `value`
- `metadata`

`value` 是当前处理的参数，而 `metadata` 是其元数据。元数据对象包含一些属性：

```typescript
export interface ArgumentMetadata {
  type: 'body' | 'query' | 'param' | 'custom';
  metatype?: Type<unknown>;
  data?: string;
}
```

这里有一些属性描述参数：

| 参数     | 描述                                                         |
| :------- | :----------------------------------------------------------- |
| type     | 告诉我们该属性是一个 body `@Body()`，query `@Query()`，param `@Param()` 还是自定义参数 [在这里阅读更多](https://docs.nestjs.cn/customdecorators)。 |
| metatype | 属性的元类型，例如 `String`。 如果在函数签名中省略类型声明，或者使用原生 JavaScript，则为 `undefined`。 |
| data     | 传递给装饰器的字符串，例如 `@Body('string')`。 如果您将括号留空，则为 `undefined`。 |

> `TypeScript` 接口在编译期间消失，所以如果你使用接口而不是类，那么 `metatype` 的值将是一个 `Object`。

### 示例

我们来关注一下 `CatsController` 的 `create()` 方法：

```ts
// cats.controller.ts
@Post()
async create(@Body() createCatDto: CreateCatDto) {
  this.catsService.create(createCatDto);
}
```

下面是 `CreateCatDto` 参数. 类型为 CreateCatDto:

```ts
export class CreateCatDto {
  name: string;
  age: number;
  breed: string;
}
```

我们要确保 `create` 方法能正确执行，所以必须验证 `CreateCatDto` 里的三个属性。我们可以在路由处理程序方法中做到这一点，但是我们会打破单个责任原则（SRP）。

+ 另一种方法是创建一个验证器类并在那里委托任务，但是不得不每次在方法开始的时候我们都必须使用这个验证器。
+ 那么验证中间件呢？ 这可能是一个好主意，但我们不可能创建一个整个应用程序通用的中间件 (因为中间件不知道 `execution context` 执行环境,也不知道要调用的函数和它的参数)。

在这种情况下，你应该考虑使用**管道**。

Nest 与 [class-validator](https://github.com/pleerock/class-validator) 配合得很好。这个优秀的库允许您使用基于装饰器的验证。装饰器的功能非常强大，尤其是与 Nest 的 Pipe 功能相结合使用时，因为我们可以通过访问 `metatype` 信息做很多事情，在开始之前需要安装一些依赖。

```
$ npm i --save class-validator class-transformer
```

安装完成后，我们就可以向 `CreateCatDto` 类添加一些装饰器。

```ts
// create-cat.dto.ts
import { IsString, IsInt } from 'class-validator';

export class CreateCatDto {
  @IsString()
  name: string;

  @IsInt()
  age: number;

  @IsString()
  breed: string;
}
```

> 在 [此处](https://github.com/typestack/class-validator#usage) 了解有关类验证器修饰符的更多信息。

现在我们来创建一个 `ValidationPipe` 类。

```ts
// validate.pipe.ts
import { PipeTransform, Injectable, ArgumentMetadata, BadRequestException } from '@nestjs/common';
import { validate } from 'class-validator';
import { plainToClass } from 'class-transformer';

@Injectable()
export class ValidationPipe implements PipeTransform<any> {
  async transform(value: any, { metatype }: ArgumentMetadata) {
    if (!metatype || !this.toValidate(metatype)) {
      return value;
    }
    const object = plainToClass(metatype, value);
    const errors = await validate(object);
    if (errors.length > 0) {
      throw new BadRequestException('Validation failed');
    }
    return value;
  }

  private toValidate(metatype: Function): boolean {
    const types: Function[] = [String, Boolean, Number, Array, Object];
    return !types.includes(metatype);
  }
}
```

> 我们已经使用了 [class-transformer](https://github.com/pleerock/class-transformer) 库。它和 [class-validator](https://github.com/pleerock/class-validator) 库由同一个作者开发，所以他们配合的很好。

让我们来看看这个代码。首先你会发现 `transform()` 函数是 **异步的**, Nest 支持**同步**和**异步**管道。这样做的原因是因为有些 `class-validator` 的验证是 [可以异步的](https://docs.nestjs.cn/typestack/class-validator?id=custom-validation-classes)(Promise)

下一步，请观察 `toValidate()` 方法。当验证类型不是 JavaScript 的数据类型时，跳过验证。

下一步，我们使用 `class-transformer` 的 `plainToClass()` 方法来转换 JavaScript 的参数为可验证的类型对象。一个请求中的 body 数据是不包含类型信息的，`Class-validator` 需要使用前面定义过的 DTO，就需要做一个类型转换。

最后，如前所述，这就是一个验证管道，它要么返回值不变，要么抛出异常。

最后一步是设置 `ValidationPipe` 管道，与 [异常过滤器](https://docs.nestjs.cn/exceptionfilters) 相同，它们可以是方法范围的、控制器范围的和全局范围的。另外，管道可以是参数范围的。我们可以直接将管道实例绑定到路由参数装饰器，例如 `@Body()`。让我们来看看下面的例子：

```ts
// cats.controller.ts
@Post()
async create(@Body(new ValidationPipe()) createCatDto: CreateCatDto) {
  this.catsService.create(createCatDto);
}
```

当验证逻辑仅涉及一个指定的参数时，参数范围的管道非常有用。要在方法级别设置管道，您需要使用 `UsePipes()` 装饰器。

```ts
@Post()
@UsePipes(new ValidationPipe())
async create(@Body() createCatDto: CreateCatDto) {
  this.catsService.create(createCatDto);
}
```

在上面的例子中 `ValidationPipe` 的实例已就地立即创建。另一种可用的方法是直接传入类（而不是实例），让框架承担实例化责任，并启用**依赖注入**。

```ts
@Post()
@UsePipes(ValidationPipe)
async create(@Body() createCatDto: CreateCatDto) {
  this.catsService.create(createCatDto);
}
```

由于 `ValidationPipe` 被创建为尽可能通用，所以我们将把它设置为一个**全局作用域**的管道，用于整个应用程序中的每个路由处理器。

```ts
// main.ts
async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.useGlobalPipes(new ValidationPipe());
  await app.listen(3000);
}
bootstrap();
```

> 在 [混合应用](https://docs.nestjs.cn/8/faq?id=混合应用) 中 `useGlobalPipes()` 方法不会为网关和微服务设置管道, 对于标准 (非混合) 微服务应用使用 `useGlobalPipes()` 全局设置管道。

全局管道用于整个应用程序、每个控制器和每个路由处理程序。就依赖注入而言，从任何模块外部注册的全局管道（如上例所示）无法注入依赖，因为它们不属于任何模块。为了解决这个问题，可以使用以下构造直接为任何模块设置管道：

```ts
// app.module.ts
import { Module } from '@nestjs/common';
import { APP_PIPE } from '@nestjs/core';

@Module({
  providers: [
    {
      provide: APP_PIPE,
      useClass: ValidationPipe
    }
  ]
})
export class AppModule {}
```

请注意使用上述方式依赖注入时，请牢记无论你采用那种结构模块管道都是全局的。另外，useClass 并不是处理自定义提供者注册的唯一方法。在 [这里](https://docs.nestjs.cn/8/fundamentals?id=custom-providers) 了解更多。

# [生命周期事件](https://docs.nestjs.cn/8/fundamentals?id=生命周期事件-1)

所有应用程序元素都有一个由 `Nest` 管理的生命周期。`Nest` 提供了**生命周期钩子**，提供了对关键生命时刻的可见性，以及在关键时刻发生时采取行动 (在你的 `module`，`injectable` 或者 `controller` 中注册代码) 的能力。

## 生命周期序列

下图描述了关键应用生命周期事件序列，从应用引导之时到 node 应用退出。我们可以把整个生命周期划分为三个阶段：初始化，运行和终止。使用生命周期，你可以合理计划模块和服务的初始化，管理活动链接，并且在应用程序收到终止指令时优雅地退出。

![生命周期钩子](https://docs.nestjs.com/assets/lifecycle-events.png)

## 生命周期事件

生命周期事件在应用初始化与终止时发生。Nest 在 `modules`，`injectables` 和 `controllers` 的以下每个生命周期事件 (首先要使能 shutdown 钩子，如下描述) 中调用注册钩子方法。和上图所示的一样，Nest 也调用合适的底层方法来监听连接，以及终止监听连接。

在下述表格中，`onModuleDestroy`, `beforeApplicationShutdown` 和 `onApplicationShutdown` 仅仅在显式调用 `app.close()` 或者应用收到特定系统信号 (例如 SIGTERM) 并且在初始化时 (参见下表的应用 `shutdown` 部分) 正确调用了 `enableShutdownHooks` 方法后被触发。

| 生命周期钩子方法              | 生命周期时间触发钩子方法调用                                 |
| :---------------------------- | :----------------------------------------------------------- |
| `OnModuleInit()`              | 初始化主模块依赖处理后调用一次                               |
| `OnApplicationBootstrap()`    | 在应用程序完全启动并监听连接后调用一次                       |
| `OnModuleDestroy()`           | 收到终止信号 (例如 SIGTERM) 后调用                              |
| `beforeApplicationShutdown()` | 在 `onModuleDestroy()` 完成 (Promise 被 resolved 或者 rejected)；一旦完成，将关闭所有连接 (调用 app.close() 方法). |
| `OnApplicationShutdown()`     | 连接关闭处理时调用 (app.close())                              |

> 上述列出的生命周期钩子没有被请求范围类触发。请求范围类并没有和生命周期以及不可预测的寿命绑定。他们为每个请求单独创建，并在响应发送后通过垃圾清理系统自动清理。

### 使用

所有应用周期的钩子都有接口表示，接口在技术上是可选的，因为它们在 `TypeScript` 编译之后就不存在了。尽管如此，为了从强类型和编辑器工具中获益，使用它们是一个很好的实践。要使用合适的接口。例如，要注册一个方法在特定类 (例如，控制器，提供者或者模块) 初始化时调用，使用 `OnModuleInit` 接口，提供 `onModuleInit()` 方法，如下：

```ts
import { Injectable, OnModuleInit } from '@nestjs/common';

@Injectable()
export class UsersService implements OnModuleInit {
  onModuleInit() {
    console.log(`The module has been initialized.`);
  }
}
```

### 异步初始化

此外，`OnModuleInit` 和 `OnApplicationBootstrap` 钩子都允许您延迟应用程序初始化过程 (返回一个 `Promise` 或在方法主体中将方法标记为 `async` 和 `await` 异步方法)。

```ts
async onModuleInit(): Promise<void> {
  await this.fetch();
}
```

## [Application Shutdown](https://docs.nestjs.cn/8/fundamentals?id=application-shutdown)

`onModuleDestroy()`, `beforeApplicationShutdown()` 和 `onApplicationShutdown()` 钩子程序响应系统终止信号 (当应用程序通过显示调用 `app.close()` 或者收到 `SIGTERM` 系统信号时)，以优雅地关闭 `Nest` 应用程序。这一功能通常用于 `Kubernetes` 、`Heroku` 或类似的服务。

系统关闭钩子消耗系统资源，因此默认是禁用的。要使用此钩子，必须通过 `enableShutdownHooks()` 激活侦听器。

```ts
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  // Starts listening to shutdown hooks
  app.enableShutdownHooks();
  await app.listen(3000);
}
bootstrap();

```

> 由于平台限制，NestJs 的关闭钩子在 Windows 下有一些限制。`SIGINT`，`SIGBREAK` 以及一些 `SIGHUP` 信号可以工作–[阅读更多](https://nodejs.org/api/process.html#process_signal_events)。然而，`SIGTERM` 在 Windows 下不工作，因为在任务管理器中关闭一个线程是无条件的。“例如，应用没有办法发现或者阻止它”。一些 Windows 下关于 `SIGINT` 和 `SIGBREAK` 的 libuv 的 [相关文档](https://docs.libuv.org/en/v1.x/signal.html)。参见 Nodejs 的 [线程信号事件](https://nodejs.org/api/process.html#process_signal_events) 文档。

`enableShutdownHooks` 开始监听时消耗内存。如果要在一个单独 Node 线程中运行多个 Nest 应用 (例如，使用多个 Jest 运行测试)，Node 会抱怨监听者太多。出于这个原因，`enableShutdownHooks` 默认未启用。要在单个 Node 进程中运行多个实例时尤其要注意这一点。

如果应用程序接收到一个终止信号，它将会依次调用注册的 `onModuleDestroy()`, `beforeApplicationShutdown()` 和 `onApplicationShutdown()` 方法，将响应信号作为第一个参数。如果一个注册函数等待异步调用 (作为 promise)，那么在 `promise` 被解析或拒绝之前，它不会关闭 Nest 应用程序。

```ts
@Injectable()
class UsersService implements OnApplicationShutdown {
  onApplicationShutdown(signal: string) {
    console.log(signal); // e.g. "SIGINT"
  }
}
```

# 跨域

  // 允许跨域

  app.enableCors();

https://icode9.com/content-4-943291.html

# 静态资源

为了像单页应用程序（ `SPA` ）一样提供静态内容，我们可以使用 `@nestjs/serve-static` 包中的 `ServeStaticModule`。

```
npm install --save @nestjs/serve-static
```

安装过程完成后，我们可以将 `ServeStaticModule` 导入根 `AppModule`，并通过将配置对象传递给 `forRoot()` 方法来配置它。

```ts
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { ServeStaticModule } from '@nestjs/serve-static';
import { join } from 'path';

@Module({
  imports: [
    ServeStaticModule.forRoot({
      rootPath: join(__dirname, '..', 'client'),
    }),
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

路径是相对于打包后的 dist 文件，所以需要多加一层回退

# NEST CLI

crud 生成器

https://docs.nestjs.com/cli/usages#nest-generate

https://docs.nestjs.cn/8/recipes?id=crud%e7%94%9f%e6%88%90%e5%99%a8

## CRUD 生成器

### 概述

想象一下真实世界中的场景，我们需要通过两个 CRUD 的终端暴露 `User` 和 `Product` 两个实体。参考最佳时间，我们为每个实体进行以下操作。

- 生成一个模块 (nest g mo) 来组织代码，使其保持清晰的界限（将相关模块分组）
- 生成一个控制器 (nest g co) 来定义 CRUD 路径（或者 GraphQL 应用的查询和变更）
- 生成一个服务 (nest g s) 来表示/隔离业务逻辑
- 生成一个实体类/接口来代表资源数据类型
- 生成数据转换对象（或者 `GraphQL` 应用输入）来决定数据如何通过网络传输

很多步骤！

为了加速执行重复步骤，`Nest CLI` 提供了一个生成器（`schematic(原理)`）可以自动生成所有的模板文件以减少上述步骤，同时让开发者感觉更易用。

> 该 `schematic` 支持生成 `HTTP` 控制器，`微服务` 控制器，`GraphQL` 处理器（代码优先或者原理优先），以及 `WebSocket` 网关等。

### 生成新资源

在项目根目录下执行以下代码来生成资源。

``` 
nest g resource
```

`nest g resource` 命令不仅仅生成所有 Nestjs 构件模块 (模块，服务，控制器类) 也生成实体类，`DTO` 类和测试 (.spec) 文件

它也自动生成了所有 CRUD 终端占位符（REST API 路径，GraphQL 查询和编译，微服务和 WebSocket 网关的消息订阅器）–一键所有内容。

### 选项

生成的资源类未与任何特定 ORM（或者数据源）绑定，以在任何项目下通用。默认地，所有方法都包含了占位符，允许你用特定项目的数据源填充。类似地，如果你需要生成 GraphQL 应用的处理器，只要在传输层选择 GraphQL（代码优先）或者 GraphQL(原理优先) 即可。

这里生成一个处理器类而不是一个 REST API 控制器：

```
$ nest g resource users

> ? What transport layer do you use? GraphQL (code first)
> ? Would you like to generate CRUD entry points? Yes
> CREATE src/users/users.module.ts (224 bytes)
> CREATE src/users/users.resolver.spec.ts (525 bytes)
> CREATE src/users/users.resolver.ts (1109 bytes)
> CREATE src/users/users.service.spec.ts (453 bytes)
> CREATE src/users/users.service.ts (625 bytes)
> CREATE src/users/dto/create-user.input.ts (195 bytes)
> CREATE src/users/dto/update-user.input.ts (281 bytes)
> CREATE src/users/entities/user.entity.ts (187 bytes)
> UPDATE src/app.module.ts (312 bytes)
```

像这样传递 `--no-spec` 参数 `nest g resource users --no-spec` 来避免生成测试文件。

# TypeORM

## TypeORM 集成

为了与 `SQL` 和 `NoSQL` 数据库集成，`Nest` 提供了 `@nestjs/typeorm` 包。`Nest` 使用 [TypeORM](https://github.com/typeorm/typeorm) 是因为它是 `TypeScript` 中最成熟的对象关系映射器 ( `ORM` )。因为它是用 `TypeScript` 编写的，所以可以很好地与 `Nest` 框架集成。

为了开始使用它，我们首先安装所需的依赖项。在本章中，我们将演示如何使用流行的 [Mysql](https://www.mysql.com/) ， `TypeORM` 提供了对许多关系数据库的支持，比如 `PostgreSQL` 、`Oracle`、`Microsoft SQL Server`、`SQLite`，甚至像 `MongoDB` 这样的 `NoSQL` 数据库。我们在本章中介绍的过程对于 `TypeORM` 支持的任何数据库都是相同的。您只需为所选数据库安装相关的客户端 `API` 库。

```
npm install --save @nestjs/typeorm typeorm mysql2
```

安装过程完成后，我们可以将 `TypeOrmModule` 导入 `AppModule` 。

```ts
// app.mulde.ts

import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';

@Module({
  imports: [
    TypeOrmModule.forRoot({
      type: 'mysql',
      host: 'localhost',
      port: 3306,
      username: 'root',
      password: 'root',
      database: 'test',
      entities: [],
      // 开启错误日志
      logging: ['error'],
      synchronize: false,
    }),
  ],
})
export class AppModule {}
```

`forRoot()` 方法支持所有 `TypeORM` 包中 `createConnection()` 函数暴露出的配置属性。其他一些额外的配置参数描述如下：

| 参数                | 说明                                                     |
| :------------------ | :------------------------------------------------------- |
| retryAttempts       | 重试连接数据库的次数（默认：10）                         |
| retryDelay          | 两次重试连接的间隔 (ms)（默认：3000）                     |
| autoLoadEntities    | 如果为 `true`,将自动加载实体 (默认：false)                 |
| keepConnectionAlive | 如果未 `true`，在应用程序关闭后连接不会关闭（默认：false) |

> 更多连接选项见 [这里](https://typeorm.io/#/connection-options)

另外，我们可以创建 `ormconfig.json` ，而不是将配置对象传递给 `forRoot()`。

```bash
{
  "type": "mysql",
  "host": "localhost",
  "port": 3306,
  "username": "root",
  "password": "root",
  "database": "test",
  "entities": ["dist/**/*.entity{.ts,.js}"],
  "synchronize": true
}
```

然后，我们可以不带任何选项地调用 `forRoot()` :

> app.module.ts

```typescript
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';

@Module({
  imports: [TypeOrmModule.forRoot()],
})
export class AppModule {}
```

>  静态全局路径 (例如 `dist/**/*.entity{ .ts,.js}` ) 不适用于 Webpack 热重载。

注意：ormconfig.json` 文件由`typeorm`库载入，因此，任何`createConnection`之外的属性都不会被应用（例如由`forRoot()`方法内部支持的属性–例如`autoLoadEntities`和`retryDelay()`)

一旦完成，`TypeORM` 的 `Connection` 和 `EntityManager` 对象就可以在整个项目中注入 (不需要导入任何模块)，例如:

```ts
// app.module.ts

import { Connection } from 'typeorm';

@Module({
  imports: [TypeOrmModule.forRoot(), PhotoModule],
})
export class AppModule {
  constructor(private readonly connection: Connection) {}
}
```

## 储存库模式

`TypeORM` 支持存储库设计模式，因此每个实体都有自己的存储库。可以从数据库连接获得这些存储库。

为了继续这个示例，我们需要至少一个实体。我们来定义 `User` 实体。

```ts
// user.entity.ts
import { Entity, Column, PrimaryGeneratedColumn } from 'typeorm';

@Entity()
export class User {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  firstName: string;

  @Column()
  lastName: string;

  @Column({ default: true })
  isActive: boolean;
}
```

> 关于实体的更多内容见 [TypeORM 文档](https://typeorm.io/#/entities)。

该 `User` 实体在 `users` 目录下。这个目录包含了和 `UsersModule` 模块有关的所有文件。你可以决定在哪里保存模型文件，但我们推荐在他们的**域**中就近创建，即在相应的模块目录中。

要开始使用 `user` 实体，我们需要在模块的 `forRoot()` 方法的选项中（除非你使用一个静态的全局路径）将它插入 `entities` 数组中来让 `TypeORM` 知道它的存在。

```ts
// app.module.ts
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';
import { User } from './users/user.entity';

@Module({
  imports: [
    TypeOrmModule.forRoot({
      type: 'mysql',
      host: 'localhost',
      port: 3306,
      username: 'root',
      password: 'root',
      database: 'test',
      entities: [User],
      synchronize: true,
    }),
  ],
})
export class AppModule {}
```

现在让我们看一下 `UsersModule`：

> Ps. nest.js 中，数据库实体命名用名词单数，service、controller、module 的命名用名词复数

```ts
// users.module.ts
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';
import { UsersService } from './users.service';
import { UsersController } from './users.controller';
import { User } from './user.entity';

@Module({
  imports: [TypeOrmModule.forFeature([User])],
  providers: [UsersService],
  controllers: [UsersController],
})
export class UsersModule {}
```

此模块使用 `forFeature()` 方法定义在当前范围中注册哪些存储库。这样，我们就可以使用 `@InjectRepository()` 装饰器将 `UsersRepository` 注入到 `UsersService` 中:

```ts
// users.service.ts

import { Injectable } from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository } from 'typeorm';
import { User } from './user.entity';

@Injectable()
export class UsersService {
  constructor(
    @InjectRepository(User)
    private usersRepository: Repository<User>
  ) {}

  findAll(): Promise<User[]> {
    return this.usersRepository.find();
  }

  findOne(id: number): Promise<User> {
    return this.usersRepository.findOne(id);
  }

  async remove(id: number): Promise<void> {
    await this.usersRepository.delete(id);
  }
}c 
```

> 不要忘记将 `UsersModule` 导入根 `AppModule`

如果要在导入 `TypeOrmModule.forFeature` 的模块之外使用存储库，则需要重新导出由其生成的提供程序。 您可以通过导出整个模块来做到这一点，如下所示：

```typescript
// users.module.ts
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';
import { User } from './user.entity';

@Module({
  imports: [TypeOrmModule.forFeature([User])],
  exports: [TypeOrmModule],
})
export class UsersModule {}
```

现在，如果我们在 `UserHttpModule` 中导入 `UsersModule` ，我们可以在后一个模块的提供者中使用 `@InjectRepository(User)`。

```typescript
// users-http.module.ts
import { Module } from '@nestjs/common';
import { UsersModule } from './user.module';
import { UsersService } from './users.service';
import { UsersController } from './users.controller';

@Module({
  imports: [UsersModule],
  providers: [UsersService],
  controllers: [UsersController],
})
export class UserHttpModule {}
```

## 关系

关系是指两个或多个表之间的联系。关系基于每个表中的常规字段，通常包含主键和外键。

关系有三种：

| 名称          | 说明                                                         |
| :------------ | :----------------------------------------------------------- |
| 一对一        | 主表中的每一行在外部表中有且仅有一个对应行。使用 `@OneToOne()` 装饰器来定义这种类型的关系 |
| 一对多/多对一 | 主表中的每一行在外部表中有一个或多的对应行。使用 `@OneToMany()` 和 `@ManyToOne()` 装饰器来定义这种类型的关系 |
| 多对多        | 主表中的每一行在外部表中有多个对应行，外部表中的每个记录在主表中也有多个行。使用 `@ManyToMany()` 装饰器来定义这种类型的关系 |

使用对应的装饰器来定义实体的关系。例如，要定义每个 `User` 可以有多个 `Photo`，可以使用 `@OneToMany()` 装饰器。

> user.entity.ts

```typescript
import { Entity, Column, PrimaryGeneratedColumn, OneToMany } from 'typeorm';
import { Photo } from '../photos/photo.entity';

@Entity()
export class User {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  firstName: string;

  @Column()
  lastName: string;

  @Column({ default: true })
  isActive: boolean;

  @OneToMany((type) => Photo, (photo) => photo.user)
  photos: Photo[];
}
```

>  要了解 TypeORM 中关系的内容，可以查看 [TypeORM 文档](https://typeorm.io/#/relations)。

## 自动载入实体

手动将实体一一添加到连接选项的 `entities` 数组中的工作会很无聊。此外，在根模块中涉及实体破坏了应用的域边界，并可能将应用的细节泄露给应用的其他部分。针对这一情况，可以使用静态全局路径（例如, dist/**/*.entity{.ts,.js})。

注意，`webpack` 不支持全局路径，因此如果你要在单一仓库 (Monorepo) 中构建应用，可能不能使用全局路径。针对这一问题，有另外一个可选的方案。在配置对象的属性中 (传递给 `forRoot()` 方法的) 设置 `autoLoadEntities` 属性为 `true` 来自动载入实体，示意如下：

> app.module.ts

```typescript
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';

@Module({
  imports: [
    TypeOrmModule.forRoot({
      ...
      autoLoadEntities: true,
    }),
  ],
})
export class AppModule {}
Copy to clipboardErrorCopied
```

通过配置这一选项，每个通过 `forFeature()` 注册的实体都会自动添加到配置对象的 `entities` 数组中。

注意，那些没有通过 `forFeature()` 方法注册，而仅仅是在实体中被引用（通过关系）的实体不能通过 `autoLoadEntities` 配置被包含。

## 事务

数据库事务代表在数据库管理系统（DBMS）中针对数据库的一组操作，这组操作是有关的、可靠的并且和其他事务相互独立的。一个事务通常可以代表数据库中的任何变更（[了解更多](https://zh.wikipedia.org/wiki/数据库事务))。

在 [TypeORM 事务](https://typeorm.io/#/transactions) 中有很多不同策略来处理事务，我们推荐使用 `QueryRunner` 类，因为它对事务是完全可控的。

首先，我们需要将 `Connection` 对象以正常方式注入：

```ts
@Injectable()
export class UsersService {
  constructor(private connection: Connection) {}
}
```

> `Connection` 类需要从 `typeorm` 包中导入

现在，我们可以使用这个对象来创建一个事务。

```typescript
async createMany(users: User[]) {
  const queryRunner = this.connection.createQueryRunner();

  await queryRunner.connect();
  await queryRunner.startTransaction();
  try {
    await queryRunner.manager.save(users[0]);
    await queryRunner.manager.save(users[1]);

    await queryRunner.commitTransaction();
  } catch (err) {
    //如果遇到错误，可以回滚事务
    await queryRunner.rollbackTransaction();
  } finally {
    //你需要手动实例化并部署一个queryRunner
    await queryRunner.release();
  }
}
```

注意 `connection` 仅用于创建 `QueryRunner`。然而，要测试这个类，就需要模拟整个 `Connection` 对象（它暴露出来的几个方法），因此，我们推荐采用一个帮助工厂类（也就是 `QueryRunnerFactory`) 并且定义一个包含仅限于维持事务需要的方法的接口。这一技术让模拟这些方法变得非常直接。

可选地，你可以使用一个 `Connection` 对象的回调函数风格的 `transaction` 方法 ([阅读更多](https://typeorm.io/#/transactions/creating-and-using-transactions))。

```typescript
async createMany(users: User[]) {
  await this.connection.transaction(async manager => {
    await manager.save(users[0]);
    await manager.save(users[1]);
  });
}
```

不推荐使用装饰器来控制事务 (`@Transaction()` 和 `@TransactionManager()`)。

## 订阅者

使用 TypeORM[订阅者](https://typeorm.io/#/listeners-and-subscribers/what-is-a-subscriber)，你可以监听特定的实体事件

```ts
import { Connection, EntitySubscriberInterface, EventSubscriber, InsertEvent } from 'typeorm';
import { User } from './user.entity';

@EventSubscriber()
export class UserSubscriber implements EntitySubscriberInterface<User> {
  constructor(connection: Connection) {
    connection.subscribers.push(this);
  }

  listenTo() {
    return User;
  }

  beforeInsert(event: InsertEvent<User>) {
    console.log(`BEFORE USER INSERTED: `, event.entity);
  }
}
```

> 事件订阅者不能是 [请求范围](https://docs.nestjs.com/fundamentals/injection-scopes) 的

现在，将 `UserSubscriber` 类添加到 `providers` 数组。

```typescript
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';
import { User } from './user.entity';
import { UsersController } from './users.controller';
import { UsersService } from './users.service';
import { UserSubscriber } from './user.subscriber';

@Module({
  imports: [TypeOrmModule.forFeature([User])],
  providers: [UsersService, UserSubscriber],
  controllers: [UsersController],
})
export class UsersModule {}Copy to clipboardErrorCopied
```

更多实体订阅者内容见 [这里](https://typeorm.io/#/listeners-and-subscribers/what-is-a-subscriber)。

## 迁移

## 测试

## 定制储存库

## 异步配置

## 示例

[这儿](https://github.com/nestjs/nest/tree/master/sample/05-sql-typeorm) 有一个可用的例子。

# 疑问

1. 怎么做到像之前的 squealize 那样，有的话就直接访问，没有的话就创建？通过 create + save
2. groupName 这种类型，我希望可以在 groups 目录下声明一次就够了，不希望到处引入，目前看来到处引入是比较常见的作法
3. `synchronize` 好像没有生效
4. Query Builder 是 typeorm 的查询语法，适用于复杂的查询
5. Query RUnenr 是创建事务用的，`QueryRunner` 的每个实例都是一个独立的数据库连接。
6. group 的 id 对我来说是没有意义的，我不需要记住哪个 group 的 id 是哪个，每次操作前先拿到 id 就行，但是以 id 作为主键，然后 id 的增长又不可控制，我觉得没有意义，我在应用层解决好了
7. 实体和表字段的风格转换是否可以统一设置
11. 如何复用 module？charator 和 seiyuu 表是类似的，所有 controller 和 service 也都是类似的
9. 为什么 partialType 这种是在 swagger 里的？ 我直接用 typescript 的语法行不行？不行，这部分属于非类型上下文
10. dto 每个都要创建一个文件吗？
11. nest 的模板信息太多，而且不同模块有很多相似的内容，如何快速更改呢？ 类似 vscdoe f2 那种感觉
12. formatListWithProject 处理应该是放在 controller？还是 service？或者是其他模块？先放在 controller 把，因为这样的话处理过程中 的类型就可以都放在一个地方，不用提取到公共区域
13. service 的实例方法是否可以互相依赖？那如果要依赖要怎么抽离公共部分呢？class 本身就是如此组织的
    1. 一个模块可以只有 service，controller 可以通过 module 嵌套，路由拼接后即可访问
    2. 可以在 service 注入其他的 service 吗？可以的，就是该在这里注入

## 通过 Class-validate 校验

属于 typeorm 的功能

```ts
@Column({
	comment: '配置 JSON',
	length: 5000,
})
@Validator.IsString({ message: '必须为字符串' })
@Validator.Length(0, 5000, { message: '长度在 0~5000' })
content: string;
```

这里遇到一个问题：新增实体时，需要校验所有字段，但更新实体时，由于性能需要，我们一般不会一次查询所有字段，就需要指定更新时，不校验没有赋值的字段，我们通过 Typeorm 的 EventSubscriber 完成数据库操作前的代码校验，并控制新增时全字段校验，更新时只校验赋值的字段，删除时不做校验：

```ts
@EventSubscriber()
export class EverythingSubscriber implements EntitySubscriberInterface<any> {
  // 插入前校验
  async beforeInsert(event: InsertEvent<any>) {
    const validateErrors = await validate(event.entity);
    if (validateErrors.length > 0) {
      throw new HttpException(getErrorMessage(validateErrors), 404);
    }
  }

  // 更新前校验
  async beforeUpdate(event: UpdateEvent<any>) {
    const validateErrors = await validate(event.entity, {
      // 更新操作不会验证没有涉及的字段
      skipMissingProperties: true,
    });
    if (validateErrors.length > 0) {
      throw new HttpException(getErrorMessage(validateErrors), 404);
    }
  }
}
```

HttpException 会在校验失败后，终止执行，并立即返回错误给客户端，这一步体现了 Nestjs 与 Typeorm 完美结合。这带来的好处就是，我们放心执行任何 CRUD 语句，完全不需要做错误处理，当校验失败或者数据库操作失败时，会自动终止执行后续代码，并返回给客户端友好的提示：

```ts
@Post()
async add(
  @Res() res: Response,
  @Body('name') name: string,
  @Body('description') description: string,
) {
  const card = await this.cardService.add(name, description);
  // 如果传入参数实体校验失败，会立刻返回失败，并提示 `@Validator.IsString({ message: '必须为字符串' })` 注册时的提示信息
  // 如果插入失败，也会立刻返回失败
  // 所以只需要处理正确情况
  res.status(HttpStatus.OK).json(card);
}
```

## 如何组织 MVC

一个 module 可以拥有多个 controller

Nestjs 开发围绕着这三个单词，Modules 是最大粒度的拆分，表示应用或者模块。Controllers 是传统意义的控制器，一个 Module 拥有多个 Controller。Provider 一般用于做 Services，比如将数据库 CRUD 封装在 Services 中，每个 Service 就是一个 Provider。

扩展阅读：https://zhuanlan.zhihu.com/p/28621374

扩展阅读 2：https://segmentfault.com/a/1190000040207165

# FAQ

## 服务器查看 Console Log

通过 npm run start:dev 启动
