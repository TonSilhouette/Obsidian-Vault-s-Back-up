## 一、异形屏适配-安全区域

1. 使用内置API`uni.getWindowInfo()`，返回值中有：
	- `pixelRatio`：设备像素比
	- `screenWidth` `screenHeight`：屏幕宽、高度
	- `windowWidth` `windowHeight`：可使用窗口宽、高度
	- `statusBarHeight`：手机状态栏的高度
	- `safeArea`：**安全区域**
		- `left`：安全区域左上角横坐标
		- `right`：安全区域右下角横坐标
		- `top`：安全区域**左上角纵坐标**
		- `bottom`：安全区域右下角纵坐标
		- `width` `height`：安全区域的宽、高度
2. 通过`uni.getWindowInfo()`API获得`safeArea`属性后，取出`safeArea`的`top`值
3. 给页面最外层的盒子添加一个动态样式，上部内边距的值设置为安全区域的`top`，即可自动、准确地留出屏幕刘海的区域
4. 每个页面都需要适配，所以将安全区域的数据储存在pinia里，页面用的时候就导入该库

```js file:src/store/system
import {defineStore} from 'pinia'
import {ref} from 'vue'
export const useSysInfoStore = defineStore('sysInfo', () => {
	const res = uni.getWindowInfo()
	const safeArea = ref(res.safeArea)
	// console.log('安全区域', safeArea);
	return {
		safeArea
	}
})
```

```js file:需要适配屏幕的页面
import { useSysInfoStore } from '@/store/system'
import { storeToRefs } from 'pinia';
const sysStore = useSysInfoStore()
const { safeArea } = storeToRefs(sysStore)
```

```html file:需要适配屏幕的页面
<view 
class="navbar" 
:style="{paddingTop: safeArea.top + 'px'}"
>
```

#### 拓展：微信右上角胶囊的适配
1. 使用内置API`uni.getMenuButtonBoundingClientRect()`，直译：获取菜单按钮边界客户端矫正（rectify / rectification），返回值中有：
	- `width` `height`：胶囊的宽、高
	- `top` `right` `bottom` `left`：胶囊的上、右、下、左边界坐标
2. 2. 通过`uni.getMenuButtonBoundingClientRect()`API取出`bottom`值
3. 给页面最外层的盒子添加一个动态样式，上部内边距的值设置为胶囊`bottom`的值，即可留出胶囊的区域
4. 每个页面都需要适配，所以将胶囊适配的数据储存在pinia里，页面用的时候就导入该库，该数据和安全区域放在同一个库里

```js file:src/store/system.js hl:7,8,11
import {defineStore} from 'pinia'
import {ref} from 'vue'
export const useSysInfoStore = defineStore('sysInfo', () => {
	const res = uni.getWindowInfo()
	const safeArea = ref(res.safeArea)
	// console.log('安全区域', safeArea);
	const capButton = uni.getMenuButtonBoundingClientRect()
	// console.log('菜单胶囊', capButton);
	return {
		safeArea,
		capButton
	}
})
```

```js file:需要适配屏幕的页面
import { useSysInfoStore } from '@/store/system'
import { storeToRefs } from 'pinia';
const sysStore = useSysInfoStore()
const { capButton } = storeToRefs(sysStore)
```

```html file:需要适配屏幕的页面
<view
class="viewport"
:style="{ paddingTop: capButton.bottom + 'px' }"
>
```

## 二、统一请求封装函数
- 小程序开发工具：wx.request + 第三方模块 → 以promise形式进行调用接口
- 小程序-uniapp：uni.request(options) + 手动封装函数
	- 函数整体围绕`const res = await uni.request(options)`这句代码，因为小程序没有axios的请求与响应拦截器，只能通过代码前后顺序控制是请求还是相应
	- 请求拦截，在代码之前：添加基地址，注入token
	- 响应拦截，在代码之后：状态码200-300（成功），数据剥离；失败，跳转至登录页

```js file:src/utils/http.js
export const http = async (options) => {
	// 1. 在发请求之前--模拟请求拦截器
	
	// 1.1 设置基地址
	const baseURL = "https://xxx";
	// 1.2 判断：如果请求地址不完整, 补全基地址；如果已经是完整的地址, 就直接使用
	if (options.url.indexOf("http") !== 0) {
		// 这里不用 === -1，是考虑到地址非开头部分出现了http的情况
		options.url = baseURL + options.url;
	}
	// 1.3 请求头注入token
	options.header = {
		...options.header,
		// 恢复原本的header
		// 额外添加token
		Authorization: 'Bearer xxx'
		// 这个项目跟后端约定, 标明客户端来源的属性
		"source-client": "miniapp",
	};
	
	// 传入什么配置, 都原封不动交给 uni.request
	// 2. 请求本体
	const res = await uni.request(options);
	
	// 3. 发请求之后，模拟响应拦截器
	if (res.statusCode >= 200 && res.statusCode < 300) {
		// 3.1 如果成功,则数据剥离
		return res.data;
	}
	if (res.statusCode === 401) {
		// 3.2 如果报401就是登录失效(或者没登录)，跳转至登录页
		uni.navigateTo({ url: "/pages/login/index" });
		return;
	}
};
```

```js title:封装接口
import {http} from '@/utils/http'
export const loginTestAPI = (phoneNumber) => {
	return http({
		url: '/login/wxMin/simple',
		method: 'post',
		data: {
			phoneNumber
		}
	})
}
```

```js title:调用接口
import { loginTestAPI } from '@/api/profile'
import { ref } from 'vue'
// 储存用户数据的变量
const profile = ref({})
// 登录函数
const loginTest = async () => {
	const { result } = await loginTestAPI('18991378888')
	profile.value = result
}
```

## 三、滚动进度条（横向）
1. 将滚动内容放入`<scroll-view>`标签中，横向滚动就加 `scroll-x`
2. 监听`@scroll`事件，滚动事件触发的回调函数中会接收一个事件参数，参数中有滚动距离
4. 引入安全区域的数据，获取设备宽度
5. 滚动距离/设备宽度，再乘以100，即可得到滚动的像素占据整个屏幕的宽度百分比
6. 将该百分比绑定滚动条的动态样式，滚动条平移占容器的百分比

```js
// 从库中引入安全区域，可以获取屏幕宽度
import { useSysInfoStore } from '@/store/system';
const sysStore = useSysInfoStore()
import { ref } from 'vue';
// 创建变量保存滚动百分比
const left = ref(0)
// 监听滚动
const onScroll = (event) => {
	// 滚动的像素占据整个屏幕的宽度百分比就是底部滚动条需要滑动的范围
	left.value = event.detail.scrollLeft / sysStore.safeArea.width * 100
}
```

```html
<scroll-view scroll-x @scroll="onScroll">
	<view>
		<!-- 滚动的内容省略 -->
	</view>
</scroll-view>
<view class="scroll-bar">
	<!-- 滚动条 -->
	<view 
	class="scroll-bar-inner" 
	:style="{transform:`translateX(${left}%)`}">
	</view>
</view>
```

## 四、上拉加载更多

1. 将滚动内容放入`<scroll-view>`标签中，纵向滚动就加 `scroll-y`
2. 监听`@scrolltolower`事件，当页面滚动到底部时会触发该事件
3. 在处理函数中，做三件事：
	1. 将page++后，调用获取数据的方法，并把新得到的数据用展开运算符拼接在已有数据后面
	2. 为了防止在数据返回前，重复上拉触发`@scrolltolower`，可以加`showLoading`并设置`mask`，数据返回时再`hideLoading`
	3. 为了防止已经到最后一页了还发请求，需要判断当前页码是否等于总页码，等于则不能再执行


 ```js
const loadMore = async () => {
	// 两种情况处理：
	// 1. 防止重复加载，加loading
	if (isLoading.value)
	return
	
	// 2. 最后一页不再加载
	if (page.value === totalPage.value)
	return uni.showToast({ title: "已经是最后一页", icon: "error" });
	
	// 每次上拉刷新前都要先将页码+1
	page.value++;
	getLikes();
};
```

## 五、下拉刷新

1. 将滚动内容放入`<scroll-view>`标签中，纵向滚动就加 `scroll-y`
2. 绑定属性`:refresher-enabled`为`true`，开启下拉刷新
3. 监听事件`@refresherrefresh`，下拉并松手时触发，在处理函数中做三件事：
	1. 声明一个变量`isRefreshing`，默认为`true`，表明正在刷新状态为正在刷新
	2. 将该界面所有请求重新再发一次，并且初始化所有变量，可以使用`Promise.all()`一起执行异步请求
	3. 将变量`isRefreshing`设置为`false`，表明刷新状态结束
4.  绑定属性`:refresher-triggered`，赋值变量`isRefreshing`，即可在请求完成时关闭刷新动画

```html
<scroll-view
scroll-y
:refresher-enabled="true" 
@refresherrefresh="refresh" 
:refresher-triggered="isRefreshing"
>
```