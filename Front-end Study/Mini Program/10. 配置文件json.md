## 全局配置--`app.json`

- 整体放在一个对象中 `{ ... }`
### 1. pages 路由配置
- 用于指定小程序由哪些页面组成，每一项都对应一个页面的路径（含文件名）信息。文件名不需要写文件后缀，框架会自动去寻找对应位置的 `.json`, `.js`, `.wxml`, `.wxss` 四个文件进行处理
- 未指定 `entryPagePath` 时，数组的第一项代表小程序的初始页面（首页）
```json
"pages": [
	"pages/index/index",
	"pages/my/index",
	"pages/profile/index",
	"pages/notify/index",
	"pages/login/index"
],
```
- 配置路由后，路由跳转才生效

### 2. window 窗口样式
- 用于设置小程序的状态栏、导航条、标题、窗口背景色

| 属性 | 默认值 | 描述 |  |
| ---- | ---- | ---- | ---- |
| navigationBarBackgroundColor | `#000000` | 导航栏背景颜色，如 `#000000` |  |
| navigationBarTextStyle | white | 导航栏标题、状态栏颜色，仅支持 `black` / `white` |  |
| navigationBarTitleText |  | 导航栏标题文字内容 |  |
| navigationStyle | default | 导航栏样式，仅支持以下值：  <br>`default` 默认样式  <br>`custom` 自定义导航栏，只保留右上角胶囊按钮。 |  |
| homeButton | default | 在非首页、非页面栈最底层页面或非tabbar内页面中的导航栏展示home键 |  |
| backgroundColor | `#ffffff` | 窗口的背景色 |  |
| backgroundTextStyle | dark | 下拉 loading 的样式，仅支持 `dark` / `light` |  |
| onReachBottomDistance | 50 | 页面上拉触底事件触发时距页面底部距离，单位为 px |  |
| pageOrientation | portrait | 屏幕旋转设置，支持`auto`/`portrait`/`landscape` |  |
| [restartStrategy](https://developers.weixin.qq.com/miniprogram/dev/reference/configuration/app.html#restartStrategy) | homePage | 重新启动策略配置 |  |
| initialRenderingCache |  | 页面[初始渲染缓存](https://developers.weixin.qq.com/miniprogram/dev/framework/view/initial-rendering-cache.html)配置，支持`static``dynamic` |  |
| visualEffectInBackground | none | 切入系统后台时，隐藏页面内容，保护用户隐私。支持 `hidden` / `none` | [2.15.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| handleWebviewPreload | static | 控制[预加载下个页面的时机](https://developers.weixin.qq.com/miniprogram/dev/framework/performance/tips/runtime_nav.html#_2-4-%E6%8E%A7%E5%88%B6%E9%A2%84%E5%8A%A0%E8%BD%BD%E4%B8%8B%E4%B8%AA%E9%A1%B5%E9%9D%A2%E7%9A%84%E6%97%B6%E6%9C%BA)。支持 `static` / `manual` / `auto` |  |

### 3. tabBar 底部导航
- 官方：设置多 tab
1. 配置样式：

| 属性 | 必填 | 默认值 | 描述 |
| ---- | ---- | ---- | ---- |
| color | 是 |  | tab 上的文字默认颜色，仅支持十六进制颜色 |
| selectedColor | 是 |  | tab 上的文字选中时的颜色，仅支持十六进制颜色 |
| backgroundColor | 是 |  | tab 的背景色，仅支持十六进制颜色 |
| borderStyle | 否 | black | tabbar 上边框的颜色， 仅支持 `black` / `white` |
| list | 是 |  | tab 的列表，详见`list`属性说明，最少2个、最多5个 |
| position | 否 | bottom | tabBar 的位置，仅支持 `bottom` / `top` |


```json
"tabBar": {
	"backgroundColor": "#fff",
	"borderStyle": "white",
	"color": "#8e8e92",
	"selectedColor": "#5591af",
	"list": [{...}]
},
```

2. 配置tabBar页面：
list 接受一个数组，**只能配置最少 2 个、最多 5 个 tab**。tab 按数组的顺序排序，每个项都是一个对象，其属性值如下：

| 属性             | 类型   | 必填 | 说明                                                                                                                           |
| ---------------- | ------ | ---- | ------------------------------------------------------------------------------------------------------------------------------ |
| pagePath         | string | 是   | 页面路径，必须在 pages 中先定义                                                                                                |
| text             | string | 是   | tab 上按钮文字                                                                                                                 |
| iconPath         | string | 否   | 图片路径，icon 大小限制为 40kb，建议尺寸为 81px * 81px，不支持网络图片  <br>**当 `position` 为 `top` 时，不显示 icon**         |
| selectedIconPath | string | 否   | 选中时的图片路径，icon 大小限制为 40kb，建议尺寸为 81px * 81px，不支持网络图片  <br>**当 `position` 为 `top` 时，不显示 icon** | 

```json
"list": [
	{
		"text": "首页",
		"pagePath": "pages/index/index",
		"iconPath": "static/tabs/home_default.png",
		"selectedIconPath": "static/tabs/home_active.png"
	},
	...
]
```
### 4. 其他配置
networkTimeout：网络超时时间

entryPagePath：小程序默认启动首页
```json
{
  "entryPagePath": "pages/index/index"
}
```

subPackages：分包结构配置 (p大小写不敏感)
```json
"subPackages": [
{
	"root": "house_pkg",
	"pages": [
		"pages/list/index",
		...
	]
},
{
	"root": "repair_pkg",
	"pages": [
		"pages/list/index",
		...
	]
},
...
```

lazyCodeLoading：配置自定义组件代码按需注入
```json
"lazyCodeLoading": "requiredComponents",
```

usingComponents：全局自定义组件配置
```json
"usingComponents": {
	"authorization": "./components/authorization/authorization"
},
```

requiredPrivateInfos：调用的地理位置相关隐私接口
```json
"requiredPrivateInfos": [
	"getLocation",
	"chooseLocation",
	...
],
```

permission：小程序接口权限相关设置
```json
"permission": {
	"scope.userLocation": {
		"desc": "你的位置信息将用于小程序位置接口的效果展示"
	}
},
```

sitemapLocation：指明 sitemap.json 的位置
```json
"sitemapLocation": "sitemap.json"
```

## 页面配置--`pageName.json`

- 页面中配置项在当前页面会覆盖 `app.json` 中相同的配置项
- 主要是单独配置`window` 属性，但这里不需要额外指定 `window` 字段

```json
{
	"usingComponents": {...},
	"navigationBarBackgroundColor": "#d2dde8"
	...
}
```