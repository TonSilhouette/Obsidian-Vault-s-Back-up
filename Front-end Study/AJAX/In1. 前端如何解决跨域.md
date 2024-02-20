## 一、什么是跨域？

在前端领域中，跨域是指浏览器允许向服务器发送跨域请求，从而克服Ajax只能**同源**使用的限制
### 同源策略
**同源策略**是一种约定，由Netscape公司1995年引入浏览器，它是浏览器最核心也最基本的安全功能，如果缺少了同源策略，浏览器**很容易受到XSS、CSFR等攻击**。所谓同源是指”**协议+域名+端口**“三者相同，即便两个不同的域名指向同一个ip地址，也非同源。

同源策略限制以下几种行为：  
- Cookie、LocalStorage 和 IndexDB 无法读取
- DOM和JS对象无法获得
- **AJAX 请求不能发送**

## 二、解决方法

### 1.跨域资源共享 (CORS)
**CORS**是一个W3C标准，全称是”跨域资源共享“（Cross-origin resource sharing）。 它允许浏览器向跨源服务器，发出XMLHttpRequest请求，从而克服了AJAX只能同源使用的限制。 CORS需要浏览器和服务器同时支持。目前，所有浏览器都支持该功能，IE浏览器不能低于IE10。

浏览器将CORS跨域请求分为**简单请求**和**非简单请求**。浏览器对这两种情况的处理是**不一样**的。

#### 简单请求
只要同时满足一下两个条件，就属于简单请求  
1. 使用下列方法之一：
	- head
	- get
	- post
2. 请求的Heder是：
	- Accept
	- Accept-Language
	- Content-Language
	- Content-Type: 只限于三个值：
		- application/x-www-form-urlencoded；
		- multipart/form-data；
		- text/plain

方法：在请求头中，增加一个Origin字段
```bash hl:2
GET /cors HTTP/1.1 
Origin: http://api.bob.com  👈🏻
Host: api.alice.com 
Accept-Language: en-US 
Connection: keep-alive 
User-Agent: Mozilla/5.0
```

后端设置：
```js
response.setHeader('Access-Control-Allow-Origin', '*')
```

#### 非简单请求
不同时满足上面的两个条件，就属于非简单请求。

非简单请求是那种对服务器有特殊要求的请求，比如请求方法是PUT或DELETE，或者Content-Type字段的类型是application/json。非简单请求的CORS请求，会在正式通信之前，增加一次HTTP查询请求，称为"预检"请求（preflight）

### 2.反向代理
前提：node + vue + webpack + webpack-dev-server 搭建的项目
```js file:vue.config.js hl:7,14-18
module.exports = {
	publicPath: '/',
	outputDir: 'dist',
	assetsDir: 'static',
	lintOnSave: process.env.NODE_ENV === 'development',
	productionSourceMap: false,
	devServer: {
		port: port,
		open: true,
		overlay: {
			warnings: false,
			errors: true
		},
		proxy: {
			'/api': { //'/api'是一个基地址标识，设置后请求将发送到本地域名的/api中
				target: 'http://localhost:3000',
				changeOrigin: true
			}
		}
	}
}
```

#### 正向代理与反向代理的区别
- 正向代理是代理客户端，为客户端服务
- 反向代理是代理服务器，为服务器端服务

#### 工作原理：
- 在Webpack的配置文件中，可以通过配置`devServer.proxy`选项来设置代理
- 代理服务器会**拦截**前端发送的请求，并将请求**转发**到目标服务器
- 在转发请求时，代理服务器会**修改请求的域名、端口等信息**，以实现跨域请求
- 目标服务器接收到代理服务器转发的请求，并返回响应结果
- 代理服务器将响应结果返回给前端应用程序

