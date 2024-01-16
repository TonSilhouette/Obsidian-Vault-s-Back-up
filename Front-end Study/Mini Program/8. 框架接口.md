## 一、getApp

- 获取到小程序全局的 `App` 实例
- 可以用来共享数据或者方法
- 不要在 `App()` 内的函数中调用，因为可以使用`this`拿到`App`实例
- 外部调用`getApp()`相当于获取了`App()`内部的`this`

```js
Page({
  getToken() {
    // 可以用变量承接
    const app = getApp()
    console.log(app.token)
    // 或者直接点语法
    console.log(getApp().token)
  },
```

## 二、getCurrentPages

- 获取当前页面栈
- 通常用来做后退按钮处理

```js
  goBack() {
    // 获取当前记录在案访问过的页面栈
    const pages = getCurrentPages()
    console.log(pages);
    // 只剩一页回首页
    if (pages.length <= 1) {
      wx.switchTab({
        url: '/pages/index/index',
      })
    } else {
      // 不然就后退
      wx.navigateBack()
    }
  },
```

面试：你曾经封装过什么功能？
答：我曾经用`getCurrentPages()`封装过后退按钮
- `getCurrentPages()`是微信小程序提供的一个框架接口，用于获取当前页面栈。调用该接口就能获取一个数组，记录着所有未被销毁的跳转页面路径
- 具体我是这样封装后退按钮的：
	- 先给按钮绑定一个`goBack()`方法，在该方法内我调用了`getCurrentPages()`接口，并且使用变量`pages`承接
	- 然后我用if，else判断`pages`的数组长度，如果长度小于等于一，那说明该页面无路可退，于是用`wx.switchTab`跳转回首页
	- 如果`pages`的长度大于一，说明该页面可以回退，直接调用`wx.navigateBack()`


## 三、Behavior

- 类似`Vue`中的`Mixin`用于组件间代码共享
- 每个 `behavior` 可以包含一组属性、数据、生命周期函数和方法。组件引用它时，它的属性、数据和方法会被合并到组件中，生命周期函数也会在对应时机被调用

封装：
```js
export default Behavior({
  data: {
    version: '1.0.3'
  },
  methods: {
    getVersion() {
      console.log('触发behavior函数');
      return this.data.version
    }
  }
})
```

使用：
```js
import myBehavior from './my-behavior.js'
Page({
  behaviors: [myBehavior],
  test() {
    console.log('触发了按钮');
    console.log(this.data.version);
    this.getVersion()
  },
```