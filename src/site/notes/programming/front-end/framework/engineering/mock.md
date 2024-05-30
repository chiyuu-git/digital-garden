---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2020-09-06-Sun, 11:47:20 am","date-modified":"2023-04-29-Sat, 8:10:16 pm","permalink":"/programming/front-end/framework/engineering/mock/","dgPassFrontmatter":true}
---


# Mock

## 概述

### 学习

+ http://mockjs.com/
+ https://github.com/nuysoft/Mock
+ 下载：`npm install mockjs –save`
+ 基于拦截 XHR，原作者已经不维护了，不再推荐学习

## 语法规范

## 数据模板定义规范 DTD

### 结构

+ **数据模板中的每个属性由 3 部分构成：属性名、生成规则、属性值：**

  ```
  // 属性名   name
  // 生成规则 rule
  // 属性值   value
  'name|rule': value
  ```

### Mock 函数

+ ```js
  Mock.mock( rurl, function( options ) )
  ```
+ 记录用于生成响应数据的函数。当拦截到匹配 rurl 的 Ajax 请求时，函数 function(options) 将被执行，并把执行结果作为响应数据返回。

## 准备 Mock 数据

### 理解 JSON 数据结构

+ 结构: 名称, 数据类型
+ value 可以变, 但结构不能变

  ```json
  “name”: ”tom”  //tom 的类型也是结构的部分，只有确定了类型之后的值，才不属于结构
  “age”:18 // “18”，结构发生了变化，算数变成了拼串
  ```

+ 编写模拟 JSON 数据: src/mock/data.json
+ mock 的数据结构和 JSON 的数据结构完全相同，那么链接后台之后只有值发生改变，可以快速的实现动态显示

### 定义 JSON 数据结构

+ 先决定 JSON 数据是对象还是数组，考虑：数据是否有顺序，对象无序，数组有序（且同类型）
+ 把 mock 数据暴露成接口，三个不同的界面，暴露成三个接口
+ 使用 mockjs 提供模拟数据，Mockjs: 用来拦截 ajax 请求, 生成随机数据返回

  > 只有值是随机的，JSON 结构不会改变

+ 在编写 JSON 的时候使用随机语法，就可以返回随机数据，如果是普通 JSON，则会正常的返回固定值

  ```js
  Mock.mock({ "number|1-100": 100 })
  {
    "employees|5-10":[
      {
        "age|20-40": 20,
        "bg|1": {
          "110000": "本科",
          "120000": "大专",
          "130000": "硕士",
          "140000": "博士"
        },
        "city": "@county(true)", //占位符语法
        "date":"@date('2018-12-dd')",
        "introduction":"@cparagraph",
        "name":"@name",
        "wage|1": {
          "110000": "5K-6K",
          "120000": "6K-8K",
          "130000": "8K-15K",
          "140000": "15K-20K"
        }
      }
    ]
  }
  ```

  ```js
  import Mock from 'mockjs'
  import data from './data.json'
  
  Mock.mock('/employee',{code:0,data:data})
  
  Mock.mock('/employee',{code:0,data:data["employees|5-10"]}) //这样的话只有会一个数据，而不会随机产生
  ```

### mockServer

+ 定义 mockServer.js

  ```js
  /*使用mockjs提供mock数据的接口*/
   import Mock from 'mockjs'
   import data from './data'
   
   //向外提供三个接口
   
   //返回goods的接口
   Mock.mock('./goods',{code:0,data:data.goods})
   //返回ratings的接口
   Mock.mock('./ratings',{code:0,data:data.ratings})
   //返回info的接口
   Mock.mock('./info',{code:0,data:data.info})
   
   /*export default {
     这里不需要暴露什么，只需要运行一下，定义的三个接口就可以访问了
   }*/
  ```

### 关联 mockServer

+ 关联 mockServer.js 在 main.js 中

  ```js
  //类似css，css也是一个模块嘛，加载一下即可一起打包、运行
   import './mock/mockServer'
  ```

+ mock 设定的路径，通过浏览器和 postman 都没有办法测试，因为这两种方式都不是发送 ajax 请求，mockjs 没有拦截操作，就不知道应该返回数据

### 定义接口函数

+ 定义接口请求函数 /api/index.js

  ```js
  //获取商家信息,都不需要代理来匹配拦截，所以不可以加/api
   export const reqShopInfo = () => ajax('/shop_info')
   //获取商家评价数组
   export const reqShopRatings = () => ajax('/shop_ratings')
   // 获取商家商品数组
   export const reqShopGoods = () => ajax('/shop_goods')
  ```
