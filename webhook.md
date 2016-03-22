WebHook
=======

Webhook 允许第三方应用监听 Coding.net 上的特定事件，在这些事件发生时通过 HTTP POST 方式通知( 超时5秒) 到第三方应用指定的 Web URL。例如项目有新的内容 Push，或是Merge Request 有更新等。
Coding.net 用户可以在自己的项目 －> 设置 －> Webhook 中创建、设置 Webhook 所需监听的事件，并配置第三方应用的 Web URL 。WebHook 可方便用户实现自动部署，自动测试，自动打包，监控项目变化等。

![](//dn-codingide.qbox.me/20150830170729.png)


目前我们支持如下的事件：

Push	: 任何时间项目内的 Push 操作

MR/PR: 任何时间项目内的 Merge Request 和 Pull Request 操作

Topic: 任何时间项目内的讨论创建，评论

Task: 任何时间项目内的任务操作

Document: 任何时间项目内的文档操作

Watch: 任何时间第三方用户关注你的项目(限公有项目)

Star: 任何时间对项目的收藏(限公有项目)


## 1. 使用

#### POST 请求 Header 说明

Webhook 的每个 POST 请求都有包含特殊的 Header

| Header |	说明 |
| :--- | --- |
| X-Coding-Event	 | 事件名（例如: push, Merge Request, Task） |

```js
HTTP HEADERS

Accept-Encoding: gzip,deflate
Via: 1.1 vegur
Content-Length: 52
Connection: close
Content-Type: application/json; charset=UTF-8
X-Request-Id: 155cb95e-6054-4d93-9b43-3764523d5cdb
Host: requestb.in
Connect-Time: 1
User-Agent: Coding.net Hook
X-Coding-Event: ping
Total-Route-Time: 0

```

首次绑定，系统会向您所填写的 url 地址，发送一个简单的请求，以验证该 WebHook 是有效的，请求如右：

    {
        "token": "123", 
        "zen": "Coding！ 让开发更简单"
    }

### Push

POST BODY

| Key | 类型 |	说明 |
| :--- | --- | --- |
| ref | String | Git Push 的分支全名。例如: head/master |
| before | String | push 前的 commit SHA |
| after | String | push 后的 commit SHA |
| repository | Object | 本次 push 操作的项目仓库信息 |
| token | String | webhook 设置的身份密钥 |

```js
    {
        "after": "daf52219b26f79ebb4e7fe367654975a141d8235", 
        "ref": "master",  
        "repository": {
            "https_url": "https://git.coding.net/kelvin/test_project.git", 
            "description": "just demo", 
            "web_url": "https://coding.net/u/kelvin/p/test_project", 
            "name": "test_project", 
            "project_id": "37915", 
            "owner":     //仓库拥有者信息
            {    
                "global_key": "kelvin", 
                "name": "kelvin", 
                "path": "/u/kelvin",  
                "avatar": "https://dn-coding-net-production-static.qbox.me/9ed11de3-65e3-4cd8-b6aa-5abe7285ab43.jpeg?imageMogr2/auto-orient/format/jpeg/crop/!209x209a0a0"    
            }, 
            "ssh_url": "git@git.coding.net:kelvin/test_project.git"
        }, 
        "before": "8e1dcdb66a4ce668706d9c774b1b0dd6d591fb52",    
        "event": "push"  // 事件类型   ,    
        "commits":      // 这次 push 包含的 commit 信息  
         [   
            {
                "sha": "daf52219b26f79ebb4e7fe367654975a141d8235",   
                "short_message": "merge" // commit 提交时注释信息,    
                "committer":      // 代码提交人信息
                {       
                    "email": "xxxxx@gmail.com" , 
                    "name": "kelvin" 
                }   
            }      
            {
                "sha": "c8069e18c0b3ea4daad74bbcdfaa2d424ac7c861", 
                "short_message": "webhook "  ,   
                "committer": {
                    "email": "xxxxx@gmail.com", 
                    "name": "kelvin"
                }
            }
        ]  , 
        "token": "xxx"  // token 需同 WebHook 设置一致
    }
```

### Member 

| Key | 类型 |	说明 |
| :--- | --- | --- |
| author | Object | 用户管理操作的发起人 |
| before | String | push 前的 commit SHA |
| target_user | Object | 本次操作的对象用户|
| action | String | 本次操作的动作，包括添加，删除用户|
| token | String | webhook 设置的身份密钥 |

```js
    {
        "author": // 用户管理操作的发起人
        {
            "global_key": "kelvin", 
            "name": "kelvin", 
            "path": "/u/kelvin", 
            "avatar": "https://dn-coding-net-production-static.qbox.me/be981e74-f8d5-4cb4-b54a-fb13a5a5787d.jpg?imageMogr2/auto-orient/format/jpeg/crop/!294x294a35a27"
        }, 
        "repository": // 仓库信息
        {
            "https_url": "https://coding.net/kelvin/test_project.git", 
            "description": "just demo", 
            "web_url": "https://coding.net/u/kelvin/p/test_project", 
            "name": "test_project", 
            "project_id": "37915", 
            "owner":  // 仓库拥有者信息
            {
                "email": "xxxxx@gmail.com", 
                "name": "kelvin"
            }, 
            "ssh_url": "git@coding.net:kelvin/test_project.git"
        }, 
        "target_user":  // 操作对象用户信息
        {
            "global_key": "desperatecat", 
            "name": "猫爷", 
            "path": "/u/desperatecat", 
            "avatar": "https://dn-coding-net-production-static.qbox.me/2b778891-e8e8-43ed-a5fc-be0f807e23eb.jpg?imageMogr2/auto-orient/format/jpeg/crop/!640x640a0a0"
        }, 
        "event": "member", 
        "action": "create" // Member 操作类型, 
        "token": "xxx"   // token 需同 WebHook 设置一致
    }
```

### Task 
| Key | 类型 |	说明 |
| :--- | --- | --- |
| author | Object | 任务操作的发起人 |
| repository | Object  | 操作的项目仓库信息 |
| target_user | Object | 本次操作的对象用户 |
| action | String | 本次操作的动作，包括创建Create，完成 Finish，重新分派 Reassign，恢复 Restore 任务|
| token | String | webhook 设置的身份密钥 |

```js
    {
        "author": // 任务操作的发起人信息
        {
            "global_key": "kelvin", 
            "name": "kelvin", 
            "path": "/u/kelvin", 
            "avatar": "https://dn-coding-net-production-static.qbox.me/be981e74-f8d5-4cb4-b54a-fb13a5a5787d.jpg?imageMogr2/auto-orient/format/jpeg/crop/!294x294a35a27"
        }, 
        "repository": // 仓库信息
        {
            "https_url": "https://coding.net/kelvin/test_project.git", 
            "description": "just demo", 
            "web_url": "https://coding.net/u/kelvin/p/test_project", 
            "name": "test_project", 
            "project_id": "37915", 
            "owner":  // 仓库拥有者信息  
            {
                "email": "xxxxx@gmail.com", 
                "name": "kelvin"
            }, 
            "ssh_url": "git@coding.net:kelvin/test_project.git"
        }, 
        "event": "task" // 事件类型 此处代表任务,  
        "action": "create" // 此处根据具体Action 类别 create finish reassign restore 略有不同 ,  
        "token": "xxx" // token 需同 WebHook 设置一致
    }
```

### Topic 

| Key | 类型 |	说明 |
| :--- | --- | --- |
| topic | Object | 讨论信息 |
| repository | Object  | 所属项目仓库信息 |
| event | String | 事件类型 |
| action | String | 本次操作的动作，包括创建, 评论 |
| token | String | webhook 设置的身份密钥 |

```js
    {
        "topic": // 讨论信息
        {
            "title": "test", 
            "web_url": "https://coding.net/u/kelvin/p/test_project/topic/22876", 
            "author": // 讨论发起人信息
            {
                "global_key": "kelvin", 
                "name": "kelvin", 
                "path": "/u/kelvin", 
                "avatar": "https://dn-coding-net-production-static.qbox.me/be981e74-f8d5-4cb4-b54a-fb13a5a5787d.jpg?imageMogr2/auto-orient/format/jpeg/crop/!294x294a35a27"
            }
        }, 
        "repository": // 所属项目仓库信息
        {
            "https_url": "https://coding.net/kelvin/test_project.git", 
            "description": "just demo", 
            "web_url": "https://coding.net/u/kelvin/p/test_project", 
            "name": "test_project", 
            "project_id": "37915", 
            "owner": {
                "email": "xxxxx@gmail.com", 
                "name": "kelvin"
            }, 
            "ssh_url": "git@coding.net:kelvin/test_project.git"
        }, 
        "event": "topic" // 事件类型为 讨论, 
        "action": "create"  // 事件操作类型为创建 , 
        "token": "xxx"   // token 需同 WebHook 设置一致
    }
```

### Document

| Key | 类型 |	说明 |
| :--- | --- | --- |
| author | Object | 文档操作的发起人 |
| repository | Object  | 所属项目仓库信息 |
| document | Object | 文档地址 |
| action | String | 本次操作的动作，包括创建Create |
| event | String | 事件类型 |
| type | String | 文档类型  |
| token | String | webhook 设置的身份密钥 |


```js
    {
        "author":  // 文档操作的发起人信息
        {
            "global_key": "kelvin",
            "name": "kelvin",
            "path": "/u/kelvin",
            "avatar": "https://dn-coding-net-production-static.qbox.me/be981e74-f8d5-4cb4-b54a-fb13a5a5787d.jpg?imageMogr2/auto-orient/format/jpeg/crop/!294x294a35a27"
        },
        "document": // 文档地址
        {
            "web_url": "https://coding.net/u/kelvin/p/test_project/attachment/default/preview/123747",
            "name": "01 Coding all.png"
        },
        "repository":  // 项目仓库信息
        {
            "https_url": "https://coding.net/kelvin/test_project.git",
            "description": "just demo",
            "web_url": "https://coding.net/u/kelvin/p/test_project",
            "name": "test_project",
            "project_id": "37915",
            "owner":   // 仓库所有人信息
            {
                "email": "xxxxx@gmail.com",
                "name": "kelvin"
            },
            "ssh_url": "git@coding.net:kelvin/test_project.git"
        },
        "event": "document" // 事件类型 document,
        "action": "upload"  // 事件操作 上传,
        "type": "file",
        "token": "xxx"  // token 需同 WebHook 设置一致
    }
```

### Star

| Key | 类型 |	说明 |
| :--- | --- | --- |
| author | Object | 收藏项目操作的发起人 |
| repository | Object  | 所属项目仓库信息 |
| event | String | 事件类型 |
| action | String | 本次操作的动作 |
| token | String | webhook 设置的身份密钥 |

```js
    {
        "author":  // 项目收藏操作发起人
        {
            "global_key": "kelvin",
            "name": "kelvin",
            "path": "/u/kelvin",
            "avatar": "https://dn-coding-net-production-static.qbox.me/be981e74-f8d5-4cb4-b54a-fb13a5a5787d.jpg?imageMogr2/auto-orient/format/jpeg/crop/!294x294a35a27"
        },
        "repository":  // 仓库信息
        {
            "https_url": "https://coding.net/kelvin/test_public.git",
            "description": "公有项目",
            "web_url": "https://coding.net/u/kelvin/p/test_public",
            "name": "test_public",
            "project_id": "69224",
            "owner":  // 仓库 owner 信息
            {
                "email": "xxxxx@gmail.com",
                "name": "kelvin"
            },
            "git_url": "git://coding.net/kelvin/test_public.git",
            "ssh_url": "git@coding.net:kelvin/test_public.git"
        },
        "event": "star"  // 事件类别关注收藏,
        "action": "star" // 事件动作收藏 ,
        "token": "xxx" // token 需同 WebHook 设置一致
    }
```

### MR/PR 操作

| Key | 类型 |	说明 |
| :--- | --- | --- |
| merge_request | Object | Merge Request 信息 |
| pull_request | Object | Pull Request 信息 |
| repository | Object  | 所属项目仓库信息 |
| event | String | 事件类型 merge_request 或者 pull_request |
| token | String | webhook 设置的身份密钥 |

```js
    {
        "merge_request":  // MR 信息
        {
            "target_branch": "master" // 目标分支, 
            "body": "* test branch" // MR 内容, 
            "title": "test MR webhook", 
            "status": "CANMERGE", 
            "source_branch": "test" // 源分支, 
            "web_url": "https://coding.net/u/kloze/p/test_project/git/merge/6", 
            "action": "create" // 动作类型, 
            "number": 6 , 
            "merge_commit_sha": "48a8e54c5be6e330b7bf5eceeef6436dd2c71262"
        }, 
        "repository": // 项目仓库信息
        {
            "https_url": "https://git.coding.net/kloze/test_project.git", 
            "description": "just demo", 
            "web_url": "https://coding.net/u/kloze/p/test_project", 
            "name": "test_project", 
            "project_id": "37915", 
            "owner":  // 仓库所有人信息
            {
                "global_key": "kloze", 
                "name": "kloze", 
                "path": "/u/kloze", 
                "avatar": "https://coding.nethttps://dn-coding-net-production-static.qbox.me/9ed11de3-65e3-4cd8-b6aa-5abe7285ab43.jpeg?imageMogr2/auto-orient/format/jpeg/crop/!209x209a0a0"
            }, 
            "ssh_url": "git@git.coding.net:kloze/test_project.git"
        }, 
        "event": "merge_request" // 此处根据具体的类型 MR 或 PR 内容略有不同, 
        "token": "xxx" // token 需同 WebHook 设置一致
    }
```


## 2. 代码示例
### 2.1 Node.js 示例代码：
```js
var express = require('express');
var bodyParser = require('body-parser');
var multer = require('multer'); 
var process = require('child_process');
var bodyParser = require('body-parser');
var multer = require('multer'); 
var app = express();

app.use(bodyParser.json()); // for parsing application/json
app.use(bodyParser.urlencoded({ extended: true })); // for parsing application/x-www-form-urlencoded
app.use(multer()); // for parsing multipart/form-data


app.post('/webhook', function(req,res){
    console.log('print', req.body);
    console.info(req.body["token"]);
    if('xxx' === req.body['token'] ){

    console.info(process);
    process.exec('git pull', {'cwd':'/home/coding/workspace'}, 
            function (error, stdout, stderr) {
                console.log('stdout========================\n' + stdout);
                console.log('stderr========================\n' + stderr);
                if (error !== null) {
                    res.send('<pre>fail!!!\n' + stdout + error + '</pre>');
                } else {
                    res.send('<pre>done!!!\n' + stdout + '</pre>');
                }
            });
    } else {
        console.log(' failed token ')
        res.send('<pre>token不正确?</pre>');
    }
});




app.set('port', 8080);

var server = app.listen( 8080, function() {
    console.log('Listening on port %d', server.address().port);
})
```

### 2.2 ruby 示例代码：

基于 sinatra 框架实现的 webhook 处理。
```js
#!/usr/bin/env ruby

require "sinatra"
require "json/stream"

  get "/" do
    'Hello world!'
  end
  post "/hook" do
    @text = request.body.read
    @data = JSON::Stream::Parser.parse(@text)
    puts @data['token']
    puts @data['ref']
    puts @data['token']
    puts @data['zen']
    puts @data['commits']
    puts @data['repository']
    puts @data['url']

    if "123" == @data['tokne']
        puts `cd workspace`
        puts `git pull `
    end
  end
  get "/env" do
    content_type :text/plain
    html = ""
    html << "System Environment:\n\n"
    ENV.each do |key, value|
        html << "#{key}: #{value}\n"
    end
    html
  end
 ```
 
### 2.3 PHP 示例代码：
```php
<?php
  $pwd = getcwd();
  $command = 'cd ' . $pwd . ' && git pull';
  $output = shell_exec($command);
  print $output;
?>
```

### 2.4 其他示例：

[在 Coding.net 部署博客并使用 WebHook 实现页面自动更新](http://segmentfault.com/a/1190000002435539)

[纷云 + Coding，专注开发者工作](https://blog.lesschat.com/lesschat-with-coding/)
