Open Coding
=======

Open Coding 是 Coding.net 的开发者开放平台，旨在为开发者提供 Coding.net 的开放资源。让开发更简单！

          

 * OpenAPI 相关说明文档&案例；
 * OAuth 第三方应用访问用户存储在 Coding 的数据；
 * Webhook 监听代码仓库并实现自动化案例；
 * 提供简单示例代码和演示项目，快速实现原型。



*注：本项目源码托管在 [Coding](https://coding.net/u/coding/p/open/), 关注本项目会收到相应通知，例如接口修改等。欢迎提交 PR。*



# 1. 使用场景



### OpenAPI

Coding.net 将网站服务封装成一系列 API（Application Programming Interface，应用编程接口）开放出去，供第三方开发者使用。Coding 欢迎各类创新应用接入，包括： 

* 游戏类应用：角色扮演、经营策略、挑战对战、社区养成等；
* 社交类应用：技术，教育，社区，资讯等；
* 其他应用：其他符合中华人民共和国相关法律法规的合法应用。

#### 网站或应用接入 Coding 开放平台后，您将获得：

丰富的优质内容：如用户信息，冒泡，项目等开放信息；

更多的开发接口：网站可以通过用户资料接口获取用户的各种数据，丰富用户注册资料，并与 Coding 开放平台实现更多互动互通。

[OpenAPI 详情 >][openapi]


### OAuth

OAuth 协议为用户资源的授权提供了一个安全的、开放而又简易的标准。OAuth2 允许第三方开发者在用户授权的前提下访问在用户在 Coding 存储的各种信息。而这种授权无需用户提供用户名和密码提供给该第三方网站。Coding 欢迎符合中华人民共和国相关法律法规的应用接入。

#### 网站或应用接入 Coding 开放平台后，您将获得：

海量的新用户：用户使用自己的 Coding 账户即可登录网站，有效减少登录交互操作，大大降低网站注册门槛，给您的网站带来海量新用户。

零成本引入流量 : 凭借 Coding 优质的用户资源，您的网站（或应用）的社交关系会更强化！

海量的开发数据：访问用户存储在 Coding 的开放数据，与 Coding 开放平台实现更多互动互通。

[OAuth 详情 >][oauth]

### Webhook

Webhook 允许第三方应用监听 Coding.net 上的特定事件，在这些事件发生时通过 HTTP POST 方式通知( 超时5秒 ) 到第三方应用指定的 Web URL。

#### 使用 Coding 的 Webhook，您可以方便地实现自动部署，自动测试，自动打包，监控项目变化等。

如项目有新的内容 Push，或是 Merge Request 有更新等。
Coding.net 用户可以在自己的项目 －> 设置 －> Webhook 中创建、设置 Webhook 所需监听的事件，并配置第三方应用的 Web URL 。

*注：为防止您填写的 URL 被第三方恶意调用，我们 Webhook 提供了一个预先填写的 Token ，
这个 Token 将会随 Post 请求信息一起发送给您，您可验证此 Token 来确认此请求确实是 Coding.net 发出的。*

[Webhook 详情 >][webhook]

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
