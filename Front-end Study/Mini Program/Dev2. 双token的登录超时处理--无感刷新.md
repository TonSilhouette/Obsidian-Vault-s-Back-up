### 大概思路
1. 登录时将`token`与`refreshToken`都储存起来
	- 存本地
	- 存`APP.js`
2. 与后端商量好，token过期或因各种原因篡改，报401
3. 在类似请求拦截器的函数中，判断报错是否为401，是的话则进入if判断进行处理
	- #优化 若token不存在也报401，则需要用**逻辑与**再判断一下请求头里是否有`Authorization`，没有则说明从未登录或已经清空了本地储存，则不需进入登录超时处理，直接进入权限判断（有无token），避免重复跳转
4. 取出`refreshToken`，发请求更新token
	- 该请求使用了自己封装的http请求，所以会经过请求拦截器，为了避免当前请求头里的`refreshToken`被注入的过期`token`覆盖，需要做以下处理：
		1. 修改请求拦截器：声明一个默认请求头`defaultHeader`，在它的`Authorization`里放入`token`。将默认请求头使用展开运算符放入拦截到的请求头里，之后把拦截的请求头再用展开运算符赋值一遍，这样可以实现：若拦截的请求里没有请求头，则使用默认请求头，即注入token；若有请求头，则该请求头会覆盖掉默认请求头
		2. 响应拦截器，在用`refreshToken`更新token的请求中，手动放入请求头`Authorization: 'Bearer ' + refreshToken`，即可避免请求拦截的覆盖
5. 把请求到的最新的`token`和`refreshToken`存起来。此时只是更新了`token`，但原先的请求仍然发送失败了，还需要再发一次请求来实现无感刷新
6. 具体的请求内容就是响应拦截的数据的`config`，所以在新的请求中直接展开运算符放入`config`。但是此时的token还是原先的那个过期的，要手动替换：
	- 在header中，展开放入header，再使用新的`Authorization`覆盖原先的`token`
7. 将这个新的请求return出去，请求的结果即可被响应拦截器返回，实现无感刷新

**refreshToken也过期的情况：** 若用户很长时间未发请求，导致refreshToken过期，就会陷入401报错的循环中。
解决方法：
- 在进入状态码为401的条件后，再判断拦截到的请求里的`url`是否含有`'/refreshToken'`，如果有则说明该请求为请求`refreshToken`的且失败了。此时应该直接跳转到登录页重新登录。
- #优化 可以在跳转登录前携带当前页面路径，登录完可以再重定向回来。

### 完整代码实现
```js
/**
* 配置请求拦截器
*/
http.intercept.request = (config) => {
	const defaultHeader = {}
	const token = wx.getStorageSync('token')
	if (token) defaultHeader.Authorization = 'Bearer ' + token
	config.header = {
		...defaultHeader,
		...config.header,
	}
	return config
}

/**
* 配置响应拦截器
*/
http.intercept.response = async ({ data, statusCode, config }) => {
	// 解构res，数据剥离并拿到状态码
	// 如果token失效，则会报错401
	if (statusCode === 401 && config.header.Authorization) {
		// refreshToken 过期的情形
		if (config.url.includes('/refreshToken')) {
			const pageStack = getCurrentPages()
			const lastPage = pageStack[pageStack.length - 1]
			const redirectURL = lastPage.route
			return wx.redirectTo({
				url: `/pages/login/index?redirectURL=/${redirectURL}`,
			})
		}
		const refreshToken = wx.getStorageSync('refreshToken')
		const res = await http({
			url: '/refreshToken',
			method: 'POST',
			header: {
				Authorization: 'Bearer ' + refreshToken,
			},
		})
		wx.setStorageSync('token', res.data.token)
		wx.setStorageSync('refreshToken', res.data.refreshToken)
		return http({
			...config,
			header: {
				...config.header,
				Authorization: 'Bearer ' + res.data.token,
			},
		})
	}
	return data
}
```
