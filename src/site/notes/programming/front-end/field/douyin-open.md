---
{"aliases":["抖音开放平台"],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2024-03-18-Mon, 4:56:20 pm","date-modified":"2024-03-19-Tue, 2:26:56 pm","permalink":"/programming/front-end/field/douyin-open/","dgPassFrontmatter":true}
---


[抖音开放平台](https://developer.open-douyin.com/docs/resource/zh-CN/dop/develop/openapi/account-permission/get-access-token)

企业身份才可以接入抖音开放平台.

|                              |                                                                                                                                                                                                                                                                                                                                                                                                                     |                                                                                                                                             |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| **名词**                       | **描述**                                                                                                                                                                                                                                                                                                                                                                                                              | **获取方式**                                                                                                                                    |
| client_key（Client Key）       | 移动/网站应用的唯一标识。                                                                                                                                                                                                                                                                                                                                                                                                       | 在应用的「**总览**」页面中获取。                                                                                                                          |
| client_secret（Client Secret） | 移动/网站应用唯一标识对应的密钥。                                                                                                                                                                                                                                                                                                                                                                                                   | 在应用的「**总览**」页面中获取。                                                                                                                          |
| code                         | 临时调用凭证，在**用户完成授权后**生成，用于获取 access_token。                                                                                                                                                                                                                                                                                                                                                                            | 获取方式，请参见 [抖音获取授权码](https://developer.open-douyin.com/docs/resource/zh-CN/dop/develop/openapi/account-permission/douyin-get-permission-code)。 |
| scope                        | 用户授权的作用域。开放平台几乎每个接口都需要特定的 Scope。                                                                                                                                                                                                                                                                                                                                                                                    | 在应用的「**能力管理**」> **xx 权限** >「**查看详情**」中获取。                                                                                                    |
| access_token                 | 接口调用凭证，用于**需要用户授权**才可以调用的接口。 access_token 有效期说明<br><br>- 当 access_token 过期（过期时间 15 天）后，可以通过该接口使用 refresh_token（过期时间 30 天）进行刷新。刷新后获得一个有效期为 15 天的 access_token，但是 refresh_token 的有效期保持不变。<br>    <br>- 若 refresh_token 过期，获取 access_token 会报错（`error_code=10010`），此时需要重新引导用户授权。<br>    <br>- 用户可以在抖音 - 我 - 设置（右上角）- 帐号与安全 - 授权管理 中取消对应用的授权，取消授权后原有 access_token 会立即失效。<br>    <br>- 抖音开放平台会定期对用户授权进行检查，取消不合规的 access_token 授权。 | 通过 [获取 access_token](https://developer.open-douyin.com/docs/resource/zh-CN/dop/develop/openapi/account-permission/get-access-token) 获取。      |
| client_token                 | 接口调用凭证，用于**不需要用户授权**就可以调用的接口。                                                                                                                                                                                                                                                                                                                                                                                       | 通过 [生成 client_token](https://developer.open-douyin.com/docs/resource/zh-CN/dop/develop/openapi/account-permission/client-token) 获取。          |
| refresh_token                | 刷新令牌，用来刷新 access_token。                                                                                                                                                                                                                                                                                                                                                                                             | 通过 [获取 access_token](https://developer.open-douyin.com/docs/resource/zh-CN/dop/develop/openapi/account-permission/get-access-token) 获取。      |

![](/img/user/programming/front-end/field/douyin-open/image-20240319142639238.png)

  1.**抖音用户**请求登录**移动/网站应用**。

  2.**移动/网站应用**向**抖音开放平台**发起**抖音用户**的授权登录请求。

  3.**抖音开放平台**向**抖音用户**请求授权。

  4.**抖音用户**同意**抖音开放平台**的授权请求。

  5.**抖音开放平台**收到**抖音用户**授权确认后，调起或重定向到**移动/网站应用**，并且附带授权临时票据（code）。

> 抖音开放平台支持 Web、sdk 和 JSB 三种渠道的授权方式

  可以通过 rpc 接口来获取 code，psm：toutiao.passport.open 调用 PsaaportOpenService/AuthorizeCode 方法 必传参数：client_key，client_secret（应用密钥），UserId，Scope（应用需要授权的授权域），AppId

  6.**移动/网站应用**调用抖音开放平台提供的 [获取 access_token](https://developer.open-douyin.com/docs/resource/zh-CN/dop/develop/openapi/account-permission/get-access-token) 接口，获取 access_token。

  https://open.douyin.com/oauth/access_token/ 必传参数：client_key，client_secret（应用密钥），code 上一步获取的 code，grant_type

  7.**抖音开放平台**通过响应参数将 access_token 返回给**移动/网站应用**。
