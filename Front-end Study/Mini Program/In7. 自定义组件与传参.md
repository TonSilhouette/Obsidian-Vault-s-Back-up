## 一、基础使用

- 小程序有许多的[内置组件](https://developers.weixin.qq.com/miniprogram/dev/component/)，比如 `view`、`image`、`scroll-view`、`swiper`等，除此之外小程序也允许开发者自定义组件
### 1. 创建组件
- 创建`components`文件夹
- 右键 "新建Component"
### 2. 注册组件
在`json`文件注册，全局组件在`app.json`，局部组件在当前组件的`json`
```js
{
  "usingComponents": {
    "navigation-bar": "/components/navigation-bar/navigation-bar",
    "authorization": "/components/authorization/authorization"
  }
}
```
### 3. 使用组件
直接将组件作为标签使用
```html
<authorization></authorization>
```

## 二、组件传值

### 1. 父传子
- 子要
```js
  properties: {
  // props的全称：属性 
    isLogin: {
      type: Boolean,
      required: true,
      value: false // 默认值使用value来声明
    }
  },
```

- 父给
```html
<authorization is-login="true"></authorization>
<authorization is-login="{{data中的布尔值}}"></authorization>
```

### 2. 子传父
- 子组件内绑定事件，通知父并传递参数
```html
<button bind:tap="childrenInformFunction"></button>
```

```js
Component({
	methods: {
		childrenInformFunction() {
		// 在这里通知父
		this.triggerEvent('eventName',payload)
		}
	}
})
```

- 父组件内监听子通知，接收参数
```html
<children bind:eventName="parentHandelFunction"></children>
```

```js
parentHandelFunction(event) {
	// 参数在event.detail中
	console.log(event.detail.payload)
}
```