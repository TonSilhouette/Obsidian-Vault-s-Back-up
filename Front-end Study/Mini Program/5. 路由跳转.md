## 一、`wx.navigateTo`

- **保留**当前页面，跳转到应用内的某个页面。但是不能跳到 tabbar 页面
- 使用`wx.navigateBack`可以返回到原页面。小程序中页面栈最多**十层**
- 路径后可以带参数，以键值对的格式

```js
goRoom(ev) {
	wx.navigateTo({
		url: `/house_pkg/pages/room/index?building=${ev.mark.building}`,
	})
}
```

## 二、`wx.navigateBack`

- 关闭**当前**页面，返回**上一页面或多级页面**
- 可通过`getCurrentPages`获取当前的页面栈，决定需要返回几层
- `delta`属性：返回的页面数 (Number)，如果 delta 大于现有页面数，则返回到首页

## 三、`wx.switchTab`

跳转到 tabBar 页面，并关闭其他**所有非 tabBar 页面**

## 四、`wx.reLaunch`

关闭**所有**页面，打开到应用内的某个页面

## 五、`wx.redirectTo`

关闭**当前**页面，跳转到应用内的某个页面。但是不允许跳转到 tabbar 页面




