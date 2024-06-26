## 一、是什么？

跟`vue`、`react`框架一样，微信小程序框架也存在生命周期，实质也是一堆会在特定时期执行的函数/钩子

小程序中，生命周期主要分成了三部分：
1. 应用的生命周期
2. 页面的生命周期
3. 组件的生命周期

![[小程序生命周期.png]]
## 二、有哪些
### 应用级别的生命周期

|生命周期|说明|
|---|---|
|onLaunch ⭐️|小程序初始化完成时触发，全局只触发一次|
|onShow ⭐️|小程序启动，或从后台进入前台显示时触发|
|onHide ⭐️|小程序从前台进入后台时触发|
|onError|小程序发生脚本错误或 API 调用报错时触发|
|onPageNotFound|小程序要打开的页面不存在时触发|
|onUnhandledRejection()|小程序有未处理的 Promise 拒绝时触发|
|onThemeChange|系统切换主题时触发|

- `onLaunch` 可以接收场景值: `options.scene`
```js
  onLaunch(options) {
	  // options.scene 场景值用来描述用户进入小程序的路径
	  console.log('小程序初始化', options);
  }
```

### 页面级别的生命周期

| 生命周期 | 说明 | 作用 |
| ---- | ---- | ---- |
| onLoad ⭐️ | 页面加载时触发 | 发送请求获取数据 |
| onShow ⭐️ | 页面显示时触发 | 请求数据 |
| onReady | 页面初次渲染完成时触发 | 获取页面元素（少用） |
| onHide ⭐️ | 页面隐藏时触发 | 终止任务，如定时器或者播放音乐 |
| onUnload | 页面卸载时触发 | 终止任务 |

- `onLoad`可以接受页面跳转参数: 编程式导航路径携带参数，`onLoad`用`options`接收
```js
  // 前一个页面跳转并带参数
  jumpTest() {
    wx.navigateTo({
      url: '/pages/testPage/testPage?a=1&b=2&id=666'
    })
  }

  // 后一个页面进行接收
  onLoad(options) {
    console.log(options);
  },
```

### 组件级别的生命周期

| 生命周期 | 说明 | 作用 |
| ---- | ---- | ---- |
| created | 组件实例刚创建好时执行（不能调用setData） | 给组件this添加自定义属性 |
| attached ⭐️ | 组件完全初始化完毕、进入页面节点树后执行 | 可以进行初始化工作 |
| detached ⭐️ | 组件离开页面节点树后执行 |  |
| error | 每当组件方法抛出错误时执行 |  |
