---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2022-09-03-Sat, 10:08:33 pm","date-modified":"2023-04-29-Sat, 8:19:28 pm","permalink":"/programming/back-end/restful/","dgPassFrontmatter":true}
---


# OPEN API

## 概述

`OpenAPI` 是一个与语言无关的 `RESTful API` 定义说明，`Nest` 提供了一个专有的模块来利用装饰器生成类似声明。

### 安装

要开始使用，首先安装依赖、

```bash
$ npm install --save @nestjs/swagger swagger-ui-express
```

如果使用 fastify，安装 `fastify-swagger` 而不是 `swagger-ui-express`:

```bash
$ npm install --save @nestjs/swagger fastify-swagger
```

### Bootstrap

安装完成后，在 `main.ts` 文件中定义并初始化 `SwaggerModule` 类:

```ts
import { NestFactory } from "@nestjs/core";
import { SwaggerModule, DocumentBuilder } from "@nestjs/swagger";
import { AppModule } from "./app.module";

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  const config = new DocumentBuilder()
    .setTitle("Cats example")
    .setDescription("The cats API description")
    .setVersion("1.0")
    .addTag("cats")
    .build();
  const document = SwaggerModule.createDocument(app, config);
  SwaggerModule.setup("api", app, document);

  await app.listen(3000);
}
bootstrap();
```

通过 `SwaggerModule#createDocument()` 方法返回的文档是一个遵循 [OpenAPI 文档](https://swagger.io/specification/#openapi-document) 的序列化对象。除了 HTTP，你也可以以 JSON/YAML 文件格式保存和使用它。

`DocumentBuilder` 建立一个遵循 OpenAPI 标准的基础文档。它提供了不同的方法来配置类似标题、描述、版本等信息属性。要创建一个完整的文档（使用 HTTP 定义），我们使用 `SwaggerModule` 类的 `createDocument()` 方法。这个方法有两个参数，一个应用实例和一个 Swagger 选项对象。我们也可以提供第三个 `SwaggerDocumentOptions` 类型可选对象，见 [文档选项](https://docs.nestjs.cn/8/openapi?id=文档选项)。

创建文档后，调用 `setup()` 方法，它接受：

1. 挂载 Swagger 界面的路径。
2. 应用实例。
3. 上述实例化的文档对象。

运行以下命令启动 HTTP 服务器。

运行以下命令启动 HTTP 服务器。

```TypeScript
$ npm run startCopy to clipboardErrorCopied
```

浏览 `http://localhost:3000/api` 可以看到 Swagger 界面。

### 文档选项

创建文档时，可以提供一些额外选项来配合库特性。这些选项应该是 `SwaggerDocumentOptions` 类型：

## 类型和参数

`SwaggerModule` 在路径处理程序上搜索所有 `@Body()`, `@Query()`, 以及 `@Param()` 装饰器来生成 API 文档。它也利用反射来创建响应模型。

不同的装饰器生成的模板也不同：

`@Body()`

![image-20210828162051986](/img/user/programming/back-end/restful/image-20210828162051986.png)

`@Query()`

![image-20210828162220084](/img/user/programming/back-end/restful/image-20210828162220084.png)

### `@ApiProperty()`

考虑以下代码：

```ts
@Post()
async create(@Body() createCatDto: CreateCatDto) {
  this.catsService.create(createCatDto);
}
```

基于 `CreateCatDto`,将创建以下 Swagger 页面模型：

![image-20210828154203461](/img/user/programming/back-end/restful/image-20210828154203461.png)

![image-20210828154329884](/img/user/programming/back-end/restful/image-20210828154329884.png)

如你所见，虽然 `CreateCatDto` 类已经声明了一些属性，但这里的定义是空的。

同时 POST 方法中也没有 user schema

要使这些类属性在 `SwaggerModule` 中可见，我们要用 `@ApiProperty()` 装饰器或使用 `CLI` 插件来自动生成：

```ts
import { ApiProperty } from "@nestjs/swagger";

export class CreateCatDto {
  @ApiProperty()
  name: string;

  @ApiProperty()
  age: number;

  @ApiProperty()
  breed: string;
}
```

> 考虑使用 Swagger 插件（参见 [CLI 插件](https://docs.nestjs.cn/8/openapi?id=cli插件)）来自动生成以代替手动装饰每个属性。

`@ApiProperty()` 装饰器也允许设置不同的原型对象属性:

```ts
@ApiProperty({
  type: Number,
  description: 'The age of a cat',
  minimum: 1,
  default: 1,
  required: false;
})
age: number;
```

> 可以使用 `@ApiPropertyOptional()` 速记装饰器来替代显式输入 `@ApiProperty({ required: false })`。

### `SchemaObject`

`@ApiProperty()` 装饰器接受一个 `SchemaObject`，常用字段如下

```ts
export interface SchemaObject {
  default?: any;
  description?: string;

  nullable?: boolean;
  discriminator?: DiscriminatorObject;
  readOnly?: boolean;
  writeOnly?: boolean;
  xml?: XmlObject;
  externalDocs?: ExternalDocumentationObject;
  example?: any;
  deprecated?: boolean;
  type?: string;
  allOf?: (SchemaObject | ReferenceObject)[];
  oneOf?: (SchemaObject | ReferenceObject)[];
  anyOf?: (SchemaObject | ReferenceObject)[];
  not?: SchemaObject | ReferenceObject;
  items?: SchemaObject | ReferenceObject;
  properties?: Record<string, SchemaObject | ReferenceObject>;
  additionalProperties?: SchemaObject | ReferenceObject | boolean;
  patternProperties?: SchemaObject | ReferenceObject | any;
  format?: string;
  title?: string;
  multipleOf?: number;
  maximum?: number;
  exclusiveMaximum?: boolean;
  minimum?: number;
  exclusiveMinimum?: boolean;
  maxLength?: number;
  minLength?: number;
  pattern?: string;
  maxItems?: number;
  minItems?: number;
  uniqueItems?: boolean;
  maxProperties?: number;
  minProperties?: number;
  required?: string[];
  enum?: any[];
}
```

### 数组

当属性是数组时，我们必须手动指定数组类型：

```ts
@ApiProperty({ type: [String] })
names: string[];
```

> 考虑使用 Swagger 插件来自动发现数组，目前看来脚手架生成的项目已经内置了自动发现数组了

要么将类型作为数组的第一个元素（如上），要么将 `isArray` 属性设为 `true`。

### 循环依赖

当你的类之间有循环依赖时，使用 `SwaggerModul` 提供的一个包含类型信息的懒函数。

```TypeScript
@ApiProperty({ type: () => Node })
```

> 考虑使用 Swagger 插件来自动发现循环依赖

### 泛型和接口

由于 `TypeScript` 没有存储泛型或者接口的元数据，当你在 DTO 中使用他们的时候，`SwaggerModule` 可能不会正确生成运行时的模型定义。基于此，下列代码不会被 Swagger 模块正确识别。

```TypeScript
createBulk(@Body() usersDto: CreateUserDto[])
```

要处理这些限制，需要显式配置类型：

```TypeScript
@ApiBody({ type: [CreateUserDto] })
createBulk(@Body() usersDto: CreateUserDto[])
```

### 枚举

要定义一个枚举，需要在 `@ApiProperty` 中用数组手动设置 `enum` 属性。

```TypeScript
@ApiProperty({ enum: ['Admin', 'Moderator', 'User']})
role: UserRole;
```

也可以如下定义一个真实的 `TypeScript` 泛型：

```TypeScript
export enum UserRole {
  Admin = 'Admin',
  Moderator = 'Moderator',
  User = 'User',
}
```

可以在 `@Query()` 参数中配合 `@ApiQuery()` 装饰器直接使用 `enum`：

```TypeScript
@ApiQuery({ name: 'role', enum: UserRole })
async filterByRole(@Query('role') role: UserRole = UserRole.User) {}
```

当 `isArray` 配置为 `true` 时, `enum` 可以多选。

`@ApiParam` 也支持同样的用法，此时 swagger 的输入 param 页面会变成 selectable

> 貌似 `@ApiProperty` 没有这个功能，因为@ApiQuery 这种是加在 congroller 上的，但是 cli 只能自动添加 apiProperty

枚举类型即是不手动赋值，选择的时候也依然可以正常的显示语义字符

### 更多

https://docs.nestjs.cn/8/openapi?id=%e7%b1%bb%e5%9e%8b%e5%92%8c%e5%8f%82%e6%95%b0

## 操作

在 OpenAPI 规范中，API 暴露的以{资源}为结束的终端，例如 `/users` 或者 `/reports/summary`,都是可以执行 HTTP 方法的，例如 `GET`,`POST` 或者 `DELETE`。

### 标签

要为控制器附加一个标签，使用`@ApiTags(…tags) 装饰器。

```TypeScript
@ApiTags('cats')
@Controller('cats')
export class CatsController {}
```

### 报头

要作为请求的一部分定义自定义报头，使用 `@ApiHeader()` 装饰器。

```TypeScript
@ApiHeader({
  name: 'X-MyHeader',
  description: 'Custom header',
})
@Controller('cats')
export class CatsController {}
```

### [响应](https://docs.nestjs.cn/8/openapi?id=响应)

### [文件上传](https://docs.nestjs.cn/8/openapi?id=文件上传)

### [扩展](https://docs.nestjs.cn/8/openapi?id=扩展)

### [高级主题：通用`ApiResponse`](https://docs.nestjs.cn/8/openapi?id=高级主题：通用apiresponse)

## [安全](https://docs.nestjs.cn/8/openapi?id=安全)

## [映射的类型](https://docs.nestjs.cn/8/openapi?id=映射的类型)

像构建 CRUD 特性一样，通常需要基于实体类型创建变体。Nest 提供了一些应用函数来进行类型变换，以让这类变换工作更简单。

### Partial 部分声明

在创建数据转换对象 (也称为 DTO),将 `创建` 和 `更新` 创建为同一类型通常很有用。例如，创建变体可能需要所有字段，但更新变体可能将所有字段都配置为可选的。

Nest 提供了 `PartialType()` 应用函数让这一任务更简单地最小化构造。

`PartialType()` 函数返回一个类型 `(类)` 将输入的所有属性配置为可选的。例如，你可以这样创建一个类型。

```TypeScript
import { ApiProperty } from '@nestjs/swagger';

export class CreateCatDto {
  @ApiProperty()
  name: string;

  @ApiProperty()
  age: number;

  @ApiProperty()
  breed: string;
}
```

默认所有的字段都是必须的。要创建一个所有字段与之相同但都是可选的字段，使用 `PartialType()` 并将 `CreateCatDto` 作为参数。

```TypeScript
export class UpdateCatDto extends PartialType(CreateCatDto) {}
```

`PartialType()` 函数从 `@nestjs/swagger` 引入.

### Pick 拾取

`PickType()` 函数从输入类型中拾取一部分属性并生成一个新类型 (类) 。假设我们起始类如下：

```TypeScript
import { ApiProperty } from '@nestjs/swagger';

export class CreateCatDto {
  @ApiProperty()
  name: string;

  @ApiProperty()
  age: number;

  @ApiProperty()
  breed: string;

```

我们使用 `PickType()` 从中拾取一部分属性：

```TypeScript
export class UpdateCatAgeDto extends PickType(CreateCatDto, ['age'] as const) {}
```

`PickType()` 函数 从 `@nestjs/swagger` 引入.

### Omit 省略

`OmitType()` 函数拾取所有输入属性，移除指定部分属性。例如，我们起始类型如下：

```TypeScript
import { ApiProperty } from '@nestjs/swagger';

export class CreateCatDto {
  @ApiProperty()
  name: string;

  @ApiProperty()
  age: number;

  @ApiProperty()
  breed: string;
}
```

我们可以以此创建一个除 `name` 之外的包含其他所有属性的类。`OmitType` 函数的第二个参数是包含要移除属性名称的数组。

```TypeScript
export class UpdateCatDto extends OmitType(CreateCatDto, ['name'] as const) {}
```

`OmitType()` 函数从 `@nestjs/swagger` 引入.

### Intersection 交叉

`IntersectionType()` 函数将两个类型组合为一个类型（类），例如，我们起始的两个类型如下：

```TypeScript
import { ApiProperty } from '@nestjs/swagger';

export class CreateCatDto {
  @ApiProperty()
  name: string;

  @ApiProperty()
  breed: string;
}

export class AdditionalCatInfo {
  @ApiProperty()
  color: string;
}Copy to clipboardErrorCopied
```

我们可以生成一个由两个类中所有属性组成的新类型。

```TypeScript
export class UpdateCatDto extends IntersectionType(
  CreateCatDto,
  AdditionalCatInfo,
) {}Copy to clipboardErrorCopied
```

`IntersectionType()` 函数从 `@nestjs/swagger` 引入.

### Composition 组合

映射类型的使用时可以组合的，例如，以下代码创建一个类型（类），它包含了 `CreateCatDto` 除了 `name` 之外的所有属性，并将所有属性设置为可选的。

```TypeScript
export class UpdateCatDto extends PartialType(
  OmitType(CreateCatDto, ['name'] as const),
) {}
```

## [装饰器](https://docs.nestjs.cn/8/openapi?id=装饰器)

所有可用的 OpenAPI 装饰器都有 Api 前缀用以和核心装饰器区分。下面是完整的装饰器名称列表以及其可能能应用的范围。

| 名称                   | 类型                |
| :--------------------- | :------------------ |
| @ApiOperation()        | Method              |
| @ApiResponse()         | Method / Controller |
| @ApiProduces()         | Method / Controller |
| @ApiConsumes()         | Method / Controller |
| @ApiBearerAuth()       | Method / Controller |
| @ApiOAuth2()           | Method / Controller |
| @ApiBasicAuth()        | Method / Controller |
| @ApiSecurity()         | Method / Controller |
| @ApiExtraModels()      | Method / Controller |
| @ApiBody()             | Method              |
| @ApiParam()            | Method              |
| @ApiQuery()            | Method              |
| @ApiHeader()           | Method / Controller |
| @ApiExcludeEndpoint()  | Method              |
| @ApiTags()             | Method / Controller |
| @ApiProperty()         | Model               |
| @ApiPropertyOptional() | Model               |
| @ApiHideProperty()     | Model               |
| @ApiExtension()        | Method              |

## [CLI 插件](https://docs.nestjs.cn/8/openapi?id=cli插件)

TypeScript 的元数据反射系统有一些限制，一些功能因此不可能实现，例如确定一个类由哪些属性组成，或者一个属性是可选的还是必须的。然而，一些限制可以在编译时强调。Nest 提供了一个增强 TypeScript 编译过程的插件来减少需要的原型代码量。

这个插件是一个 `opt-in`，你也可以选择手动声明所有的装饰器，或者仅仅声明你需要的。

### [概述](https://docs.nestjs.cn/8/openapi?id=概述)

Swagger 插件可以自动：

- 使用 `@ApiProperty` 注释所有除了用 `@ApiHideProperty` 装饰的 DTO 属性。
- 根据问号符号确定 `required` 属性 (例如 `name?: string` 将设置 `required: false`)
- 根据类型配置 `type` 为 `enum`(也支持数组)
- 基于给定的默认值配置默认参数
- 基于 `class-validator` 装饰器配置一些验证策略 (如果 `classValidatorShim` 配置为 `true`)
- 为每个终端添加一个响应装饰器，包括合适的状态和类型（响应模式）
- 根据注释生成属性和终端的描述 (如果 `introspectComments` 配置为 `true`)
- 基于注释生成属性的示例数据 (如果 `introspectComments` 配置为 `true`)

注意，你的文件名必须有如下后缀: `['.dto.ts', '.entity.ts']` (例如 `create-user.dto.ts`) 才能被插件分析。

如果使用其他后缀，你可以调整插件属性来指定 `dtoFileNameSuffix` 选项 (见下文)。

之前，如果你想要通过 Swagger 提供一个交互体验，你必须复制大量代码让包知道你的模型/组件在该声明中。例如，你可以定义一个 `CreateUserDto` 类：

```TypeScript
export class CreateUserDto {
  @ApiProperty()
  email: string;

  @ApiProperty()
  password: string;

  @ApiProperty({ enum: RoleEnum, default: [], isArray: true })
  roles: RoleEnum[] = [];

  @ApiProperty({ required: false, default: true })
  isEnabled?: boolean = true;
}
```

在中等项目中这还不是问题，但是一旦有大量类的话这就变得冗余而难以维护。

要应用 Swagger 插件，可以简单声明上述类定义：

```ts
export class CreateUserDto {
  email: string;
  password: string;
  roles: RoleEnum[] = [];
  isEnabled?: boolean = true;
}
```

插件可以自动生成所有缺失的 swagger 属性，但是如果你要覆盖他们，只需要通过 `@ApiProperty()` 显式声明即可。

### 注释自省

注释自省特性使能后，CLI 插件可以基于注释生成描述和示例值。

例如，一个给定的 `roles` 属性示例：

```TypeScript
/**
 * A list of user's roles
 * @example ['admin']
 */
@ApiProperty({
  description: `A list of user's roles`,
  example: ['admin'],
})
roles: RoleEnum[] = [];
```

你必须复制描述和示例值。当 `introspectComments` 使能后，CLI 插件可以自动解压这些注释并提供描述（以及示例，如果定义了的话）。现在，上述属性可以简化为：

```TypeScript
/**
 * A list of user's roles
 * @example ['admin']
 */
roles: RoleEnum[] = [];
```

### 使用 CLI 插件

要使能 CLI 插件，打开 `nest-cli.json` (如果你在用 [Nest CLI](https://docs.nestjs.com/cli/overview)) 并添加以下插件配置：

```TypeScript
{
  "collection": "@nestjs/schematics",
  "sourceRoot": "src",
  "compilerOptions": {
    "plugins": ["@nestjs/swagger"]
  }
}
```

你可以使用其他 `options` 属性来自定义插件特性。

```TypeScript
"plugins": [
  {
    "name": "@nestjs/swagger",
    "options": {
      "classValidatorShim": false,
      "introspectComments": true
    }
  }
]
```

`options` 属性实现以下接口：

```TypeScript
export interface PluginOptions {
  dtoFileNameSuffix?: string[];
  controllerFileNameSuffix?: string[];
  classValidatorShim?: boolean;
  introspectComments?: boolean;
}
```

| 选项                     | 默认                      | 说明                                                                                                     |
| :----------------------- | :------------------------ | :------------------------------------------------------------------------------------------------------- |
| dtoFileNameSuffix        | [‘.dto.ts’, ‘.entity.ts’] | DTO (数据传输对象) 文件后缀                                                                               |
| controllerFileNameSuffix | .controller.ts            | 控制文件后缀                                                                                             |
| classValidatorShim       | true                      | 如果配置为 `true`，模块将重用 `class-validator` 验证装饰器 (例如 `@Max(10) ` 将在 `schema` 定义中增加 `max: 10`) |
| introspectComments       | false                     | 如果配置为 `true`,插件将根据描述注释生成说明和示例                                                        |

如果不使用 CLI，但是使用一个用户定义的 `Webpack` 配置，可以和 `ts-loader` 配合使用该插件：

```TypeScript
getCustomTransformers: (program: any) => ({
  before: [require('@nestjs/swagger/plugin').before({}, program)]
}),
```

### [和`ts-jest`(e2e)](https://docs.nestjs.cn/8/openapi?id=和ts-jeste2e)

## 更多

https://docs.nestjs.cn/8/openapi?id=%e5%85%b6%e4%bb%96%e7%89%b9%e6%80%a7
