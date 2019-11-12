# 前端开发

本章节介绍前端开发中常见的问题以及解决方案，欢迎补充

## 跨域

本章简单介绍一下什么是跨域以及如何解决跨域

### 什么是跨域

跨域基本是每一个前端工程师开发中都会遇到的问题，跨域的基本概念和原因这里简单概括下，协议域名端口三者任一不一致都会导致跨域。需要清楚的是跨域限制是浏览器给我们的安全策略的限制，在服务端是没有该限制的。

### 如何解决跨域

大部分网络上的答案都是让后端协助来修改设置`Access-Control-Allow-Origin`,这里我们不介绍这种方式，只介绍在几乎没有后端帮助的情况下如何解决跨域

#### JSONP

使用JSONP是前端解决跨域最快的方式，但是这种方式仍然需要后端小小支持下，这里我们给一个最简单的JSONP实现方式

```js
var url = `https://api.test.com?jsoncallback=jsonpCb`
var script = document.createElement('script')
script.src = url
document.getElementsByTagName('body')[0].appendChild(script)
function jsonpCb (res) {
    console.log(`接口数据${res}`)
}
```

#### chrome插件

[Access-Control-Allow-Origin](https://chrome.google.com/webstore/detail/allow-cors-access-control/lhobafahddgcelffkeicbaginigeejlf?hl=zh-CN)
本质是给接口的response header中添加`Access-Control-Allow-Origin: *`, 通过chrome api实现

#### Node代理

大部分前端框架提供给你的proxy功能本质上都是使用了`webpack-dev-server`的proxy功能，而`webpack-dev-server`的proxy功能本质上是本地启动了一个Node服务来实现请求的转发
我们也可以自己用`egg|koa`框架来创建一个简单的本地Node服务

```js
// koa.js
const Koa = require('koa')
const router = require('koa-router')
const app = new Koa()
router.get('/api/getInfo', async (ctx) => {
    const res = await http.get('http://api.test.com/getInfo')
    ctx.body = res
})
app
  .use(router.routes())
  .use(router.allowedMethods())
app.listen(3000)

// fe.js
fetch('http://api.test.com/getInfo') 替换为 fetch(`http://localhost:3000/api/getInfo`)
```