# API

## 1.登陆

登陆采用 OAuth2 协议，改协议为用户资源的授权提供了一个安全的、开放而又简易的标准。OAuth2 允许第三方开发者在用户授权的前提下访问在用户在 UCenter 存储的各种信息。而这种授权无需用户提供用户名和密码提供给该第三方网站。
![图片](https://dn-coding-net-production-pp.qbox.me/e3dc0e6d-6bc6-481f-9d7e-42d351d95ab3.png)

### 1.1 授权流程

+ 将用户引导到 UCenter 第三方登录页面上。如右侧：

```
https://ucenter.szjlxh.com/oauth/authorize?client_id={client_id}
&redirect_uri={redirect_uri}&response_type=code[&scope={scope}]
```
+ 如用户未登录 UCenter，跳转到登录页面

![图片](https://dn-coding-net-production-pp.qbox.me/1400b79c-059f-4ea3-9c9e-c3e0a564ca3a.png)

+ 用户登录，并对应用请求的 `scope` 进行授权。

![图片](https://dn-coding-net-production-pp.qbox.me/fc41b43a-2225-41f7-86f1-39b1e96d078e.png)


+ 授权通过，Ucenter 会将**授权码**回传给应用在 Coding 注册的回调地址`（http://xxx.com/callback?code=xxx）`，应用直接获取授权码 `code` 即可。

![图片](https://dn-coding-net-production-pp.qbox.me/5381b5f2-002b-4e79-bdba-a45d79ea2e88.png)

+ 应用向 UCenter 的 Token Endpoint 发送请求：

```
https://ucenter.szjlxh.com/api/oauth/accessToken?client_id={client_id}&client_secret=
{client_secret}&grant_type=authorization_code&redirect_uri={redirect_uri}&code={code}
```

**Response:（点击代码展开）**
```js
{
    "code": 1,
    "message": "获取access_token成功",
    "data": {
        "access_token": "AynyRZKKskMBs4ONjOHUecgAyM2rqpvToo0QTXPA",
        "token_type": "Bearer",
        "expires_in": 3600,
        "refresh_token": "mcQNthVcEJpn09MObyxXerv4tiQq9I2z85NAe2ye"
    }
}
```

+ Response body 中的 `code` 为 0，表示正常接收请求。如果 `code` 为 1，表示请求异常，请参照 `msg` 中的返回信息进行处理。

+ 使用 `access_token` 访问受保护的资源
`https://ucenter.szjlxh.com/api/user/info?access_token={access_token}`

### 1.2 授权方式

## 2. 用户

|scope 名称| 说明 | 授权 Action 默认只读 |
|:--:|:--:|:-----:|
| user | 授权获取用户信息（用户名称，头像，tag，email，动态 ）| 读( Read ) |
| user:email | 授权获取用户的email ）| 读( Read ) |
| notification | 授权读取通知信息，包含email通知 | 读写( ReadWrite ) |
| social | 授权读取冒泡列表，好友列表 | 读( Read ) |
| social:tweet | 授权发送冒泡，冒泡操作（点赞、评论、删除）| 读写( ReadWrite ) |
| social:message |	授权读取、发送私信、私信语音 | 读写( ReadWrite ) |
| project | 授权项目信息、项目列表，仓库信息，公钥列表、成员，任务列表 | 读( Read ) |
| project:members | 授权项目管理者增、删、改项目成员，退出项目 | 读写( ReadWrite )|
| project:task | 授权任务操作，包含增、删、改 | 读写( ReadWrite ) |
| project:file | 授权文件，包含增、删、改 | 读写( ReadWrite ) |
| project:depot | 获取 commit 信息，分支操作，MR/PR, LineNotes, fork, webhook 等操作 |	读写( ReadWrite ) |
| project:key |	授权操作部署公钥、个人公钥	| 读写( ReadWrite ) |



### 2.1 获取用户信息
`GET   /api/user/info`

| 参数 | 类型 | 必填 | 说明 |
| ----- | ----- | ---- | ----- |
| acess_token | string | Y | access_token |
| user_id | int | N | 用户ID |

**Response:（点击代码展开）**
```js
{
    "code": 1,
    "message": "获取用户信息成功",
    "data": {
        "user_id": 1002,
        "username": "1227403052",
        "email": "1@qq.com",
        "phone": "",
        "details": {
            "position": {
                "title": "职位",
                "value": ""
            },
            "address": {
                "title": "地址",
                "value": ""
            },
            "department": {
                "title": "部门",
                "value": ""
            },
            "school": {
                "title": "学校",
                "value": ""
            },
            "sex": {
                "title": "性别",
                "value": ""
            },
            ...
        }
    }
}
```

### 2.2 修改用户信息
`PUT   /api/user/info`

| 参数 | 类型 | 必填 | 说明 |
| ----- | ----- | ---- | ----- |
| acess_token | string | Y | access_token |
| username | string | N | 用户名 |
| email | string | N | 邮箱 |
| phone | string | N | 手机号 |
| position等 | string | N | details中的字段 |

**Response:（点击代码展开）**
```js
{
    "code": 1,
    "message": "修改成功",
    "data": {
        "user_id": 1002,
        "username": "1227403052",
        "email": "1@qq.com",
        "phone": "",
        "details": {
            "position": {
                "title": "职位",
                "value": ""
            },
            "address": {
                "title": "地址",
                "value": ""
            },
            "department": {
                "title": "部门",
                "value": ""
            },
            "school": {
                "title": "学校",
                "value": ""
            },
            "sex": {
                "title": "性别",
                "value": ""
            },
            ...
        }
    }
}
```

## 3. 角色

### 3.1 获取用户角色

`GET   /api/user/role`

| 参数 | 类型 | 必填 | 说明 |
| ----- | ----- | ---- | ----- |
| acess_token | string | Y | access_token |

**Response:（点击代码展开）**
```js
{
    "code": 1,
    "message": "获取角色成功",
    "data": {
        "user_id": 1000,
        "roles": [
            {
                "id": 2,
                "name": "developer",
                "title": "开发者",
                "permissions": [
                    {
                        "id": 2,
                        "name": "create-app",
                        "title": "创建应用"
                    }
                ]
            }
        ]
  }
}
```

## 4. 权限

### 4.1 获取用户权限

`GET   /api/user/permission`

| 参数 | 类型 | 必填 | 说明 |
| ----- | ----- | ---- | ----- |
| acess_token | string | Y | access_token |

**Response:（点击代码展开）**
```js
{
    "code": 1,
    "message": "获取权限成功",
    "data": {
        "user_id": 1000,
        "permissions": [
            {
                "id": 39,
                "name": "create-permission",
                "title": "创建权限"
            },
            {
                "id": 38,
                "name": "create-app",
                "title": "创建应用"
            },
            {
                "id": 35,
                "name": "create-user",
                "title": "创建应用"
            },
            {
                "id": 36,
                "name": "update-user",
                "title": "修改用户"
            },
            {
                "id": 37,
                "name": "delete-user",
                "title": "删除用户"
            }
        ]
    }
}
```


## 5. 短信

### 5.1 发送验证码

`POST   /api/sms/code`

| 参数 | 类型 | 必填 | 说明 |
| ----- | ----- | ---- | ----- |
| acess_token | string | Y | access_token |
| phone | string | Y | 手机号 |

**Response:（点击代码展开）**
```js
{
    "code": 1,
    "message": "发送成功",
    "data": {
    }
}
```

### 5.2 验证验证码

`PUT   /api/sms/code`

| 参数 | 类型 | 必填 | 说明 |
| ----- | ----- | ---- | ----- |
| acess_token | string | Y | access_token |
| phone | string | Y | 手机号 |
| code | string | Y | 验证码 |

**Response:（点击代码展开）**
```js
{
    "code": 1,
    "message": "验证成功",
    "data": {
}
```
