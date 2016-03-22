# OAuth
1. 注册应用无需审核即可使用授权。
2. 采用 OAuth 2.0 协议认证。


## 1.接入

![图片](https://dn-coding-net-production-pp.qbox.me/e3dc0e6d-6bc6-481f-9d7e-42d351d95ab3.png) 

### 1.1 开发者注册
在**账户 -> 应用管理**页面添加应用，注册要接入 Coding 的应用。

![图片](https://dn-coding-net-production-pp.qbox.me/1700b8e8-b35a-4f13-9ded-63923113e423.png) 

![图片](https://dn-coding-net-production-pp.qbox.me/7fc56566-66e4-4b17-bcf6-418ad0bf51d4.png) 

其中**回调地址**是用户授权后，Coding 回调到应用，并且回传**授权码**的地址。

保存后，会返回到应用列表。

![图片](https://dn-coding-net-production-pp.qbox.me/0fe3b9c3-7827-4325-bd4f-c35458620261.png) 

此时再查看应用，会生成 `client id` 和 `client secret`。

![图片](https://dn-coding-net-production-pp.qbox.me/de705cd8-5cc7-4607-a2a7-4775f09c65e3.png) 

保存 `client id` 和 `client secret`，以便后续认证使用。

### 1.2 授权流程

+ 将用户引导到 Coding 第三方登录页面上。如右侧：

```
https://coding.net/oauth_authorize.html?client_id={client_id}
&redirect_uri={redirect_uri}&response_type=code[&scope={scope}]
```
+ 如用户未登录 Coding，跳转到登录页面

![图片](https://dn-coding-net-production-pp.qbox.me/1400b79c-059f-4ea3-9c9e-c3e0a564ca3a.png) 

+ 用户登录，并对应用请求的 `scope` 进行授权。

![图片](https://dn-coding-net-production-pp.qbox.me/fc41b43a-2225-41f7-86f1-39b1e96d078e.png) 


+ 授权通过，Coding 会将**授权码**回传给应用在 Coding 注册的回调地址`（http://xxx.com/callback?code=xxx）`，应用直接获取授权码 `code` 即可。

![图片](https://dn-coding-net-production-pp.qbox.me/5381b5f2-002b-4e79-bdba-a45d79ea2e88.png) 

+ 应用向 Coding 的 Token Endpoint 发送请求： 

```
https://coding.net/api/oauth/access_token?client_id={client_id}&client_secret=
{client_secret}&grant_type=authorization_code&code={code} 
```

返回值 ：
```
{ expires_in: "271645",  refresh_token: "xxxxxx", access_token:"xxxxxx" }
```

![图片](https://dn-coding-net-production-pp.qbox.me/c0b6eed6-1b98-4ff7-9416-51b60bf2ff41.png) 

+ 使用 `access_token` 访问受保护的资源  
`https://coding.net/api/current_user?access_token={access_token}`

![图片](https://dn-coding-net-production-pp.qbox.me/fda15723-76c4-4a97-9a22-c1e0943dd719.png) 

+ Response body 中的 `code` 为 0，表示正常接收请求。如果 `code` 为 1，表示请求异常，请参照 `msg` 中的返回信息进行处理。

![图片](https://dn-coding-net-production-pp.qbox.me/b1729148-185e-4aca-a05a-cf4a127f8b7d.png) 


## 2. 参数

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



### 2.1.0 用户信息 
`GET   /api/account/current_user`  

**Response:（点击代码展开）**
```js
{
"code": 0,
"data": 
{
"tags_str": "",
"tags": "",
"job": 0,
"sex": 0, // 0 男 1 女 2 未知
"phone": "",
"birthday": "1985-01-01",
"location": "",
"company": "",
"slogan": "",
"introduction": "",
"avatar": "/static/fruit_avatar/Fruit-15.png",
"gravatar": "https://dn-coding-net-avatar.qbox.me/89628160-f594-4e52-8c77-fda089fcc7dd.jpg",
"lavatar": "/static/fruit_avatar/Fruit-15.png",
"created_at": 1426579942000,
"last_logined_at": 1426579942000,
"last_activity_at": 1426580037625,
"global_key": "baoti",
"name": "baoti",
"name_pinyin": "",
"updated_at": 1426579942000,
"path": "/u/baoti",
"status": 1, // 0 未激活 1 已激活
"email": "niuniu@yahoo.com",
"is_member": 0,
"id": 85544,
"follows_count": 2,
"fans_count": 3,
"tweets_count": 6,
"followed": false,
"follow": false
}
}
```

### 2.2.0 发冒泡
`POST   /api/social/tweet`  


| 表单字段 | 数据类型 | 说明 |
| ----- | ----- | ----- | 
| content | string | 冒泡内容，必填 |
| device | string | 冒泡来源设备，选填 |
| location | string | 地点，选填 |
| coord | string | 坐标，选填 |
| address | address | 地址，选填 | 


**Response:（点击代码展开）**
```js
{
"code": 0,
"data": 
{
"id": 27020,
"owner_id": 84337,
"created_at": 1426585007031,
"likes": 0,
"comments": 0,
"device": "",
"location": "",
"coord": "",
"content": "<p>testing<img class=\"emotion emoji\" src=\"https://coding.net/static/emojis/grin.png\" title=\"grin\"><br> <a href=\"https://dn-coding-net-production-pp.qbox.me/f8338ca1-890c-4be2-bfda-a9eb890ab1b1.jpg\" target=\"_blank\" class=\"bubble-markdown-image-link\" rel=\"nofollow\"><img src=\"https://dn-coding-net-production-pp.qbox.me/f8338ca1-890c-4be2-bfda-a9eb890ab1b1.jpg\" alt=\"图片\" class=\" bubble-markdown-image\"></a></p>",
"path": "/u/baoti/pp/27020",
"activity_id": 0,
"liked": false
}
}
```


### 2.3.0 用户的项目列表
`GET    /api/user/projects`  


**Response:（点击代码展开）**
```js
{
"code": 0,
"data": 
{
"page": 1,
"pageSize": 100,
"totalPage": 1,
"totalRow": 2,
"list": 
[
{
"created_at": 1426246044000,
"backend_project_path": "/user/baoti/project/Coding-API",
"description": "Coding 的 API 是啥样呢？瞧一瞧，看一看。\n注意：由于官方未提供 API 文档，此处 API 整理自 Coding-Android 项目源码。\n尚未对照 IOS 客户端源码。请前往演示, 或转至 http://coding-api.coding.io/ 以查看当前整理的 API.",
"git_url": "git://coding.net/baoti/Coding-API.git",
"ssh_url": "git@coding.net:baoti/Coding-API.git",
"is_public": true,
"https_url": "https://coding.net/baoti/Coding-API.git",
"id": 67965,
"name": "Coding-API",
"owner_id": 84337,
"owner_user_name": "baoti",
"owner_user_picture": "/static/fruit_avatar/Fruit-2.png",
"owner_user_home": "<a href=\"https://coding.net/u/baoti\">baoti</a>",
"project_path": "/u/baoti/p/Coding-API",
"status": 1,
"type": 1,
"updated_at": 1426576642885,
"last_updated": 1426576630625,
"fork_count": 0,
"star_count": 2,
"watch_count": 5,
"pin": false,
"depot_path": "/u/baoti/p/Coding-API/git",
"forked": false,
"un_read_activities_count": 0,
"icon": "/static/project_icon/scenery-23.png",
"current_user_role_id": 100,
"current_user_role": "owner",
"stared": false,
"watched": false,
"recommended": 1,
"max_member": 10,
"groupId": 0
},
{
"created_at": 1426171134000,
"backend_project_path": "/user/baoti/project/Coding-Android",
"description": "Fork 自官方仓库，并添加了自己的修改分支",
"git_url": "git://coding.net/baoti/Coding-Android.git",
"ssh_url": "git@coding.net:baoti/Coding-Android.git",
"is_public": true,
"https_url": "https://coding.net/baoti/Coding-Android.git",
"id": 67604,
"name": "Coding-Android",
"owner_id": 84337,
"owner_user_name": "baoti",
"owner_user_picture": "/static/fruit_avatar/Fruit-2.png",
"owner_user_home": "<a href=\"https://coding.net/u/baoti\">baoti</a>",
"project_path": "/u/baoti/p/Coding-Android",
"status": 1,
"type": 1,
"updated_at": 1426485162000,
"last_updated": 1426485163000,
"fork_count": 307,
"star_count": 0,
"watch_count": 0,
"pin": false,
"depot_path": "/u/baoti/p/Coding-Android/git",
"forked": true,
"un_read_activities_count": 0,
"icon": "/static/project_icon/scenery-17.png",
"current_user_role_id": 100,
"current_user_role": "owner",
"stared": false,
"watched": false,
"recommended": 0,
"max_member": 10,
"groupId": 0,
"parent_depot_path": "coding/Coding-Android"
}
]
}
}
```


### 2.4.0 创建任务
`POST   /api/user/{user_name}/project/{project_name}/task`   


| 参数 | 数据类型 | 说明 |
| ----- | ----- | ----- | 
| user_name | string | 项目所有者在 Coding 的用户名，即 global_key |
| project_name | string | 项目名称 |


| 表单字段 | 数据类型 | 说明 |
| ----- | ----- | ----- | 
| owner_id | string | 当前任务执行人的 id |
| priority | int | 任务优先级, 默认值为 1, 0 - 有空再看, 1 - 正常处理, 2 - 优先处理, 3 - 十万火急 |
| deadline | string | 任务完成期限, 选填 |
| description | string | 任务描述, 选填 |
| content | string | 任务内容, 选填 |


**Response:（点击代码展开）**
```
{
"code": 0
}
```


### 2.4.1 根据 id 获取任务 
`GET   /api/user/{user_name}/project/{project_name}/task/{task_id}`   


| 参数 | 数据类型 | 说明 |
| ----- | ----- | ----- | 
| user_name | string | 项目所有者在 Coding 的用户名，即 global_key |
| project_name | string | 项目名称 |
| task_id | int | 任务 id |


**Response:（点击代码展开）**
```js
{
"code": 0,
"data": 
{
"id": 123,
"priority": 2,
"comments": 0,
"content": "",
"created_at": 1426579942000,
"creator": {
"avatar": "/static/fruit_avatar/Fruit-15.png",
"gravatar": "https://dn-coding-net-avatar.qbox.me/89628160-f594-4e52-8c77-fda089fcc7dd.jpg",
"lavatar": "/static/fruit_avatar/Fruit-15.png",
"global_key": "baoti",
"name": "baoti",
"id": 85544,
},
"creator_id": 85544,
"current_user_role_id": "2",
"owner": {
"avatar": "/static/fruit_avatar/Fruit-15.png",
"gravatar": "https://dn-coding-net-avatar.qbox.me/89628160-f594-4e52-8c77-fda089fcc7dd.jpg",
"lavatar": "/static/fruit_avatar/Fruit-15.png",
"global_key": "baoti",
"name": "baoti",
"id": 85544,
},
"owner_id": 85544,
"project": {
"id": 67965,
"name": "Coding-API",
"owner_id": 84337,
},
"project_id": 67965,
"status": 1,
"updated_at": 1426579942000,
"deadline": "",
"has_description": true
}
}
```


### 2.4.2 根据 id 删除任务 
`DELETE   /api/user/{user_name}/project/{project_name}/task/{task_id}`   


| 参数 | 数据类型 | 说明 |
| ----- | ----- | ----- | 
| user_name | string | 项目所有者在 Coding 的用户名，即 global_key |
| project_name | string | 项目名称 |
| task_id | int | 任务 id |


**Response:（点击代码展开）**
```
{
"code": 0
}
```


### 2.4.3 更新任务 
`PUT   /api/user/{user_name}/project/{project_name}/task/{task_id}`   


| 参数 | 数据类型 | 说明 |
| ----- | ----- | ----- | 
| user_name | string | 项目所有者在 Coding 的用户名，即 global_key |
| project_name | string | 项目名称 |
| task_id | int | 任务 id |


| 表单字段 | 数据类型 | 说明 |
| ----- | ----- | ----- | 
| content | string | 任务内容, 可不填 |
| status | short | 任务内容, 1 - 进行中, 2 - 已完成, 可不填 |
| owner_id | int | 任务新执行者的 id, 可不填 |
| description | string | 任务描述, 可不填 |
| priority | int | 任务优先级, 默认值为 1, 0 - 有空再看, 1 - 正常处理, 2 - 优先处理, 3 - 十万火急, 可不填 |
| deadline | string | 任务完成期限, 格式 "yyyy-MM-dd" |


**Response:（点击代码展开）**
```
{
"code": 0
}
```


### 2.4.4 获取用户某项目的任务列表 
`GET  /api/user/{user_name}/project/{project_name}/tasks/my/{task_status}`   


| 参数 | 数据类型 | 说明 |
| ----- | ----- | ----- | 
| user_name | string | 项目所有者在 Coding 的用户名，即 global_key |
| project_name | string | 项目名称 |
| task_status | string | 任务状态, "all" - 获取所有任务, "processing" - 获取进行中的任务, "done" - 获取已完成的任务, "watch" - 获取关注的任务 |


**Response:（点击代码展开）**
```js
{
"code": 0,
"data": 
{
"page": 1,
"pageSize": 100,
"totalPage": 1,
"totalRow": 2,
"list": 
[
{
"id": 123,
"priority": 2,
"comments": 0,
"content": "",
"created_at": 1426579942000,
"creator": {
"avatar": "/static/fruit_avatar/Fruit-15.png",
"gravatar": "https://dn-coding-net-avatar.qbox.me/89628160-f594-4e52-8c77-fda089fcc7dd.jpg",
"lavatar": "/static/fruit_avatar/Fruit-15.png",
"global_key": "baoti",
"name": "baoti",
"id": 85544,
},
"creator_id": 85544,
"current_user_role_id": "2",
"owner": {
"avatar": "/static/fruit_avatar/Fruit-15.png",
"gravatar": "https://dn-coding-net-avatar.qbox.me/89628160-f594-4e52-8c77-fda089fcc7dd.jpg",
"lavatar": "/static/fruit_avatar/Fruit-15.png",
"global_key": "baoti",
"name": "baoti",
"id": 85544,
},
"owner_id": 85544,
"project": {
"id": 67965,
"name": "Coding-API",
"owner_id": 84337,
},
"project_id": 67965,
"status": 1,
"updated_at": 1426579942000,
"deadline": "",
"has_description": true
},
{
"id": 125,
"priority": 2,
"comments": 0,
"content": "",
"created_at": 1426579942000,
"creator": {
"avatar": "/static/fruit_avatar/Fruit-15.png",
"gravatar": "https://dn-coding-net-avatar.qbox.me/89628160-f594-4e52-8c77-fda089fcc7dd.jpg",
"lavatar": "/static/fruit_avatar/Fruit-15.png",
"global_key": "baoti",
"name": "baoti",
"id": 85544,
},
"creator_id": 85544,
"current_user_role_id": "2",
"owner": {
"avatar": "/static/fruit_avatar/Fruit-15.png",
"gravatar": "https://dn-coding-net-avatar.qbox.me/89628160-f594-4e52-8c77-fda089fcc7dd.jpg",
"lavatar": "/static/fruit_avatar/Fruit-15.png",
"global_key": "baoti",
"name": "baoti",
"id": 85544,
},
"owner_id": 85544,
"project": {
"id": 67965,
"name": "Coding-API",
"owner_id": 84337,
},
"project_id": 67965,
"status": 1,
"updated_at": 1426579942000,
"deadline": "",
"has_description": true
}
]
}
}
```


### 2.5.0 webhook 列表 
`GET   /api/user/{user_name}/project/{project_name}/git/hooks`   


| 参数 | 数据类型 | 说明 |
| ----- | ----- | ----- | 
| user_name | string | 项目所有者在 Coding 的用户名，即 global_key |
| project_name | string | 项目名称 |


**Response:（点击代码展开）**
```js
{
"code":0,
"data":	
[
{
"depot_id":20658,
"hook_url":"http://abc.coding.net",
"type":1,
"token":"",
"status":0,
"created_at":1431511538877,
"updated_at":1431511538877,
"send_type":3,
"id":174
},
{
"depot_id":20658,
"hook_url":"https://api-doc.coding.io",
"type":1,
"token":"hjklzxcqaw3422345",
"status":0,
"created_at":1431511574722,
"updated_at":1431511574722,
"send_type":3,
"id":175
}
]
}
```


### 2.5.1 增加 webhook 
`POST   /api/user/{user_name}/project/{project_name}/git/hook/{hook_id}`   


| 参数 | 数据类型 | 说明 |
| ----- | ----- | ----- | 
| user_name | string | 项目所有者在 Coding 的用户名，即 global_key |
| project_name | string | 项目名称 |


| 表单字段 | 数据类型 | 说明 |
| ----- | ----- | ----- | 
| hook_url | string | webhook 链接 |
| token | string | 自定义 webhook 秘钥 |
| type_push | boolean | push代码 通知开关 |
| type_mr_pr | boolean | MR/PR 通知开关 |
| type_topic | boolean | 发布讨论 通知开关 |
| type_member | boolean | 成员变动 通知开关 |
| type_comment | boolean | 发表评论 通知开关 |
| type_document | boolean | 文档 通知开关 |
| type_watch | boolean | 项目被关注 通知开关 |
| type_star | boolean | 项目被加星 通知开关 |
| type_task | boolean | 项目任务 通知开关 |



**Response:（点击代码展开）**
```
{
"code": 0
}
```


### 2.5.2 获取 webhook 
`GET   /api/user/{user_name}/project/{project_name}/git/hook/{hook_id}`    


| 参数 | 数据类型 | 说明 |
| ----- | ----- | ----- | 
| user_name | string | 项目所有者在 Coding 的用户名，即 global_key |
| project_name | string | 项目名称 |
| hook_id | int | webhook id |


**Response:（点击代码展开）**
```js
{
"code":0,
"data":	
{
"depot_id":20658,
"hook_url":"http://abc.coding.net",
"type":1,
"token":"",
"status":0,
"created_at":1431511538877,
"updated_at":1431511538877,
"send_type":3,
"id":174
}
}
```


### 2.5.3 编辑 webhook 
`PUT   /api/user/{user_name}/project/{project_name}/git/hook/{hook_id}`    


| 参数 | 数据类型 | 说明 |
| ----- | ----- | ----- | 
| user_name | string | 项目所有者在 Coding 的用户名，即 global_key |
| project_name | string | 项目名称 |
| hook_id | int | webhook id |


| 表单字段 | 数据类型 | 说明 |
| ----- | ----- | ----- | 
| hook_url | string | webhook 链接 |
| token | string | 自定义 webhook 秘钥 |
| type_push | boolean | push代码 通知开关 |
| type_mr_pr | boolean | MR/PR 通知开关 |
| type_topic | boolean | 发布讨论 通知开关 |
| type_member | boolean | 成员变动 通知开关 |
| type_comment | boolean | 发表评论 通知开关 |
| type_document | boolean | 文档 通知开关 |
| type_watch | boolean | 项目被关注 通知开关 |
| type_star | boolean | 项目被加星 通知开关 |
| type_task | boolean | 项目任务 通知开关 |



**Response:（点击代码展开）**
```
{
"code": 0
}
```


### 2.5.4 删除 webhook 
`DELETE   /api/user/{user_name}/project/{project_name}/git/hook/{hook_id}`    


| 参数 | 数据类型 | 说明 |
| ----- | ----- | ----- | 
| user_name | string | 项目所有者在 Coding 的用户名，即 global_key |
| project_name | string | 项目名称 |
| hook_id | int | webhook id |



**Response:（点击代码展开）**
```
{
"code": 0
}
```


### 2.6.0 从 branch 名称查询 commit sha 
`GET   /api/user/{user_name}/project/{project_name}/git/tree/{branch_name}`   


| 参数 | 数据类型 | 说明 |
| ----- | ----- | ----- | 
| user_name | string | 项目所有者在 Coding 的用户名，即 global_key |
| project_name | string | 项目名称 |
| branch_name | string | 分支名称 |


**Response:（点击代码展开）**
```js
{
"code":0,
"data": 
{
"ref":"master",
"lastCommitter": 
{
"name":"ZXX_ABC",
"email":"kcccss111@gmail.com",
"avatar":"https://dn-coding-net-production-static.qbox.me/0174e523-963b-4cfb-a2ef-d4f0efea3465.jpg?imageMogr2/auto-orient/format/jpeg/crop/!128x128a0a0",
"link":"/u/zxx_sse"
},
"files":
[
{
"mode":"file",
"path":"README.md",
"name":"README.md",
"flatten_path":"README.md"
}
],
"can_edit":true,
"isHead":true,
"headCommit":
{
"fullMessage":"new file abc.md",
"shortMessage":"new file abc.md\n",
"allMessage":"",
"commitId":"8a305b5304a0eab0c58ea861dbb1b760a72ea9a8",
"commitTime":1431057342000,
"committer":
{
"name":"ZXX_ABC",
"email":"kcccss111@gmail.com",
"avatar":"https://dn-coding-net-production-static.qbox.me/0174e523-963b-4cfb-a2ef-d4f0efea3465.jpg?imageMogr2/auto-orient/format/jpeg/crop/!128x128a0a0",
"link":"/u/zxx_sse"
},
"notesCount":0
},
"readme": 
{
"data":"#abcawerq\n",
"lang":"markdown",
"size":10,
"previewed":true,
"preview":"\u003ch1\u003eabcawerq\u003c/h1\u003e",
"lastCommitMessage":"Initial commit\n",
"lastCommitDate":1431057300000,
"lastCommitId":"48b65a3b85ab65974f4d64da0f27d7e757561198",
"lastCommitter": 
{
"name":"ZXX_ABC",
"email":"kcccss111@gmail.com",
"avatar":"https://dn-coding-net-production-static.qbox.me/0174e523-963b-4cfb-a2ef-d4f0efea3465.jpg?imageMogr2/auto-orient/format/jpeg/crop/!128x128a0a0",
"link":"/u/zxx_sse"
},
"mode":"file",
"path":"README.md",
"name":"README.md",
"flatten_path":"README.md"
},
"lastCommit": 
{
"fullMessage":"new file abc.md",
"shortMessage":"new file abc.md\n",
"allMessage":"",
"commitId":"8a305b5304a0eab0c58ea861dbb1b760a72ea9a8",
"commitTime":1431057342000,
"committer": 
{
"name":"ZXX_ABC",
"email":"kcccss111@gmail.com",
"avatar":"https://dn-coding-net-production-static.qbox.me/0174e523-963b-4cfb-a2ef-d4f0efea3465.jpg?imageMogr2/auto-orient/format/jpeg/crop/!128x128a0a0",
"link":"/u/zxx_sse"
},
"notesCount":0
}
}
}
```



### 2.7.0 生成部署公钥 
`POST   /api/user/{user_name}/project/{project_name}/git/deploy_key`  


| 参数 | 数据类型 | 说明 |
| ----- | ----- | ----- | 
| user_name | string | 项目所有者在 Coding 的用户名，即 global_key |
| project_name | string | 项目名称 |


| 表单字段 | 数据类型 | 说明 |
| ----- | ----- | ----- | 
| title | string | 公钥名 |
| content | string | 公钥内容 |


**Response:（点击代码展开）**
```js
{
"code":0,
"data":
{
"id":2781,
"owner_id":31828,
"content":"ssh-rsa qwertyuiopas...... qwertyuiopasdfghjkl",
"finger_print":"ef:03:d4:9d:d3:7d:bb:6d:ac:c3:99:9a:10:a9:32:4d",
"type":"deploy",
"title":"coding-abc",
"created_at":1431509572347
}
}
```



### 2.8.0 读取 repo 某个文件
`GET  /api/user/{user_name}/project/{project_name}/git/blob/{branch_name}/{file_path}`


| 参数 | 数据类型 | 说明 |
| ----- | ----- | ----- | 
| user_name | string | 项目所有者在 Coding 的用户名，即 global_key |
| project_name | string | 项目名称 |
| branch_name | string | 分支名称 |
| file_path | string | 文件在 repo 中的路径 ( 包含文件名 ) |


**Response:（点击代码展开）**
```js
{
code: 0,
data: {
ref: "master",
can_edit: false,
isHead: true,
file: {
data: "Hello World!!!",//文件内容
lastCommitMessage: "增加 README.md 介绍启动方式 ",
lastCommitDate: 1430963621000,
lastCommitId: "25f84dafb0124c98d3da475e1afe7719603314c4",
lastCommitter: {
name: "ZXX_ABC",
email: "xxx@xxx.com",
avatar: "https://dn-coding-net-production-static.qbox.me/xxxxxxxx",
link: "/u/zxx_sse"
},
mode: "file",
path: "README.md",
name: "README.md"
},
headCommit: {
fullMessage: "Accept Merge Request #4560 : (upgrade_project_update_depot -> master)",
shortMessage: "Accept Merge Request #4560 : (upgrade_project_update_depot -> master) ",
allMessage: "",
commitId: "0a82a0e0dfc6e6c268b6dd61742b15478924e4b3",
commitTime: 1431589531000,
committer: {
name: "Jeffrey",
email: "xxx@xxx.net",
avatar: "https://dn-coding-net-production-static.qbox.me/xxxxxx",
link: "/u/ojf"
},
notesCount: 0
}
}
}
```
