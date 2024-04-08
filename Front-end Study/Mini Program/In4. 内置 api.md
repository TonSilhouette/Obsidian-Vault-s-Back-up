## 网络请求 ：`wx.request`

`wx.request` API 是用来发起网络请求的，类似于网页中的 `ajax`
- 小程序无浏览器环境，无XHR（XMLHttpRequest）对象，故无法使用`ajax`
- `wx.request`接受一个 `Object` 类型的参数，这个参数支持按需指定以下字段来接收接口调用结果：

| 参数名      | 说明                                 |
| -------- | ---------------------------------- |
| success  | 接口调用成功的回调函数，类似try                  |
| fail     | 接口调用失败的回调函数，类似catch                |
| complete | 接口调用结束的回调函数（调用成功、失败都会执行），类似finally |

- 基础库 [2.10.2](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) 版本起，异步 API 支持 callback & promise 两种调用方式。当接口参数 Object 对象中不包含 success/fail/complete 时将默认返回 promise，否则仍按回调方式执行，无返回值，`wx.request`的 promisify 需要开发者自行封装

```js
// pages/index/index.js // 小程序发起网络请求（调用接口）的方法 
wx.request({ 
	// 接口地址 
	url: 'api/path/xxx', 
	// 请求的方法 
	method: 'GET｜POST|PUT', 
	// 请求时的数据 
	data: {}, 
	success: (res) => {
	// 此处使用箭头函数是为了避免this执行受影响
	}
})
```

## 轻提示 ：`wx.showToast`

- 显示消息提示框
- **以 [Promise 风格](https://developers.weixin.qq.com/miniprogram/dev/framework/app-service/api.html#%E5%BC%82%E6%AD%A5-API-%E8%BF%94%E5%9B%9E-Promise) 调用**：支持

| 属性       | 类型       | 默认值     | 必填  | 说明                           |
| -------- | -------- | ------- | --- | ---------------------------- |
| title    | string   |         | 是   | 提示的内容                        |
| icon     | string   | success | 否   | 图标                           |
| image    | string   |         | 否   | 自定义图标的本地路径，image 的优先级高于 icon |
| duration | number   | 1500    | 否   | 提示的延迟时间                      |
| mask     | boolean  | false   | 否   | 是否显示透明蒙层，防止触摸穿透              |
| success  | function |         | 否   | 接口调用成功的回调函数                  |
| fail     | function |         | 否   | 接口调用失败的回调函数                  |
| complete | function |         | 否   | 接口调用结束的回调函数                  |

其中`icon`属性的合法值：

| 合法值  | 说明                                                          |
| ------- | ------------------------------------------------------------- |
| success | 显示成功图标，此时 title 文本最多显示 7 个汉字长度 |
| error   | 显示失败图标，此时 title 文本最多显示 7 个汉字长度 |
| loading | 显示加载图标，此时 title 文本最多显示 7 个汉字长度 |
| none    | 不显示图标，此时 title 文本最多可显示两行|


```js
wx.showToast({ 
	title: 'title', 
	icon：'none'
})
```

## 加载 ：`wx.showLoading`&`wx.hideLoading`

- 显示加载提示框
- 需主动调用 `wx.hideLoading` 才能关闭提示框
- **以 [Promise 风格](https://developers.weixin.qq.com/miniprogram/dev/framework/app-service/api.html#%E5%BC%82%E6%AD%A5-API-%E8%BF%94%E5%9B%9E-Promise) 调用**：支持

```js
  getData(){
    //显示加载中状态
    wx.showLoading({
      title: '正在加载中',
      // 添加遮罩, 阻止页面的其他交互
      mask: true
    })
    wx.request({
      url: 'https://mock.boxuegu.com/mock/3293/students',
      success: (res) => {
        // do something
        // 隐藏加载中状态
        wx.hideLoading()
      }
    })
  }
```

## 本地储存（同步） `wx.xxStorageSync`

- `wx.setStorageSync` 在本地存入一个数据
	- 该数据可以为复杂数据类型，无需格式转化
- `wx.getStorageSync` 读取本地的一个数据
- `wx.removeStorageSync` 删除本地存储的一个数据
- `wx.clearStorageSync` 清空本地存储的数据
