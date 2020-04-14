## my gatsby

🚀 基于 Gatsby 开发自己的 blog

### 插件 🔥

- gatsby-remark-vscode // 代码高亮插件
- antd // 蚂蚁金服框架
- typography-theme-github // Typography.js 主题
- office-ui-fabric-react // 微软的极简 UI 框架
- gitalk //基于 github 开源评论系统
- react-cplayer // react 音乐播放器
- spark-md5 // 十六进制 md5 为 comment 系统 提供不超过 50 位的 主键
- netlify-cms-app // netlify 自动化发布部署
- gatsby-plugin-baidu-analytics // 百度统计插件

### other

- 百度云统计: `https://tongji.baidu.com/`
- 百度链接提交: autoBaiduSubmit,

```
百度链接提交入口 http://zhanzhang.baidu.com/linksubmit/url
Google网站登录口：https://www.google.com/webmasters/tools/submit-url
360搜索引擎登录入口：http://info.so.360.cn/site_submit.html
搜狗网站收录提交入口:http://www.sogou.com/feedback/urlfeedback.php
必应网站提交登录入口：http://www.bing.com/toolbox/submit-site-url
```

### 自动化部署

> 服务器 Nodejs 利用 Github 的 Webhook 功能来完成

- Nodejs
- npm install -g github-webhook-handler // github push 的时候回调
- shell 执行 Linux 脚本
- pm2 Nodejs 进程守护程序
- git
- Nginx

利用 webhook 来 执行 git pull => yarn build

`Nginx sever root` 暴露 build 的目录

#### Code

install nodejs yarn git github-webhook-handle pm2

- Nodes Deploy.js

```js
var http = require("http")
// github-webhook-handler 的绝对路径
var createHandler = require("/usr/lib/node_modules/github-webhook-handler")
var handler = createHandler({ path: "/", secret: "xxx" })
// 上面的 secret 保持和 GitHub 后台设置的一致

function run_cmd(cmd, args, callback) {
  var spawn = require("child_process").spawn
  var child = spawn(cmd, args)
  var resp = ""

  child.stdout.on("data", function(buffer) {
    resp += buffer.toString()
  })
  child.stdout.on("end", function() {
    callback(resp)
  })
}

http
  .createServer(function(req, res) {
    handler(req, res, function(err) {
      res.statusCode = 404
      res.end("no such location")
    })
  })
  .listen(7777) // 启动服务的端口，需要开放安全组

handler.on("error", function(err) {
  console.error("Error:", err.message)
})

handler.on("push", function(event) {
  console.log(
    "Received a push event for %s to %s",
    event.payload.repository.name,
    event.payload.ref
  )
  run_cmd("sh", ["./deploy.sh", event.payload.repository.name], function(text) {
    console.log(text)
  })
})
```

- shell

```shell
 #!/bin/bash

WEB_PATH='/root/githubWebhook/warehouse/my-Gatsby-Blog'
# WEB_USER='root'
# WEB_USERGROUP='root'

echo "开始执行shell"
cd $WEB_PATH
echo "pulling source code..."
git pull
echo "changing permissions..."
#chown -R $WEB_USER:$WEB_USERGROUP $WEB_PATH
echo " git pull 完成. 开始 build"
yarn run gatsby build
echo "build 完成"
```

#### PM2

- 最简单的启用一个应用:pm2 start app.js
- 停止：pm2 stop app_name|app_id
- 删除：pm2 delete app_name|app_id
- 重启：pm2 restart app_name|app_id
- 停止所有：pm2 stop all
- 查看所有的进程：pm2 list
- 查看所有的进程状态：pm2 status
- 查看某一个进程的信息：pm2 describe app_name|app_id

[参考链接](https://www.jianshu.com/p/3de4e8f15621)

## 坑

- typeof window !== 'undefined' && <Cplayer />

  > 组件中使用了 window 全局对象，build 时会报错，处理方式 判断 window 来渲染组件

- 删除 public 文件后 或者删除又恢复等 差不多等操作 进行`gatsby develop && build`会出现静态资源 404 等现象 .
  > 这是因为 Gatsby 等构建是基于 .cach 来构建的。public 被删除后会出现构建生产不一致的情况 ，出现这种情况 删除 .cach 重新构建一遍
