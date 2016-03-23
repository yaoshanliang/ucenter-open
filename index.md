概述
=======

用户中心 UCenter，集“统一身份认证服务”、“用户系统”、“权限系统”、“日志系统”、“文件系统”、“邮件服务”、“短信服务”等于一体，
旨在为开发者提供统一的管理、调用服务，让开发更简单！


*注：本项目源码托管在Github: [用户中心](https://github.com/yaoshanliang/ucenter) -
[开放平台](https://github.com/yaoshanliang/ucenter-open), 关注本项目会收到相应通知，例如接口修改等。欢迎提交 PR。*



# 1. 接入指南


### OAuth

OAuth 协议为用户资源的授权提供了一个安全的、开放而又简易的标准。OAuth2 允许第三方开发者在用户授权的前提下访问在用户在 UCenter 存储的各种信息。而这种授权无需用户提供用户名和密码提供给该第三方网站。

#### 网站或应用接入 Ucenter 开放平台后，您将获得：

新用户：用户使用自己的 UCenter 账户即可登录网站，有效减少登录交互操作，大大降低网站注册门槛，给您的网站带来海量新用户。


[OAuth 详情 >][oauth]


# 2. 使用案例

### OpenAPI
为方便您的测试，我们用 Swagger Ui 构建了可执行的文档，您可以在线输入参数进行测试，如下图：

![](https://dn-coding-net-production-pp.qbox.me/e8653816-e4da-48f5-b613-74600eda8b94.png)

绝大部分接口的响应类型为 application/json。此时，响应的JSON对象中都包含一个 code 字段，这用于标识请求成功与否。


code 为 0，此时成功执行了请求。 对应的数据位于data字段。 它的具体类型跟接口有关。
``` html
{"code": 0, "data": []}
```

code 不为 0，此时请求未能正常完成。 对应的错误信息位于msg字段。 它的类型为object。
``` html
{"code": 999, "msg": {"j_captcha": "缺少验证码"}}
```

您也可以使用命令测试，如以查看用户是否存在为例，{"code":0} 即为存在：
``` bash
$ curl -i https://coding.net/api/account/check?key=niuniu@yahoo.com

HTTP/1.1 200 OK
Server: nginx
Date: Tue, 11 Aug 2015 07:24:37 GMT
Content-Type: application/json;charset=UTF-8
Content-Length: 10
Access-Control-Allow-Methods: GET, POST, PUT, DELETE, OPTIONS
Access-Control-Max-Age: 3600
Access-Control-Allow-Headers: Origin, X-Requested-With, Content-Type, Accept
Access-Control-Allow-Credentials: true
Cache-Control: no-cache, no-store
Set-Cookie: sid=ae025dee-d1d4-4470-a162-399853430069; Domain=.coding.net; Path=/
; Secure; HttpOnly
Set-Cookie: sid=; Path=/; Max-Age=0; Secure; HttpOnly

{"code":0}
```


[OpenAPI 详情 >][openapi]

### OAuth

注册应用无需审核即可使用授权。当用户同意并授权应用访问 Coding 数据后。
可以使用 OAuth 相关的 API 获取用户数据。

``` bash
GET /api/account/current_user
```

[OAuth 详情 >][oauth]


### Webhook

利用 Webhook 可实现如项目演示自动更新。(演示内容自动在 push 后生成新的页面）

其步骤如下：

（A）用户打开客户端以后，客户端要求用户给予授权。

（B）用户同意给予客户端授权。

（C）客户端使用上一步获得的授权，向认证服务器申请令牌。

（D）认证服务器对客户端进行认证以后，确认无误，同意发放令牌。

（E）客户端使用令牌，向资源服务器申请获取资源。

（F）资源服务器确认令牌无误，同意向客户端开放资源。


[Webhook 详情 >][webhook]



[dist]:https://coding.net/u/coding/open
[openapi]:/openapi
[oauth]:/oauth.html
[webhook]:/webhook.html
[opencoding]:https://coding.net/u/coding/open
