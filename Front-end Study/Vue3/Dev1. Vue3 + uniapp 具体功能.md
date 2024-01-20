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

## 四、电商分类模块

- 发请求获取分类模块的所有数据，数据结构为一个数组，数组的每一项又是一个数组，对应着一级目录与每个一级目录的二级目录，其中，每个二级目录中又有商品的数组
- 一级标题的渲染就直接遍历数据的第一层即可
- 二级标题的渲染与切换我使用了**计算属性**，通过一级标题的选中动态计算出二级数组
	- 其中，获取一级标题的选中状态，是通过声明一个变量`activeId`，点击了哪个一级标题就把当前的`id`赋值给`activeId`
	- 再根据`activeId`在计算属性中 find `id`并 return 出二级数组，同时还使用动态类来切换一级标题的高亮
- 二级标题使用`v-for`渲染后，再二次`v-for`二级标题下的商品，在列表渲染中要注意`item`不能重名，以及每一层循环中的`item`在数组中代表什么数据

```js
// 计算属性
const subCategories = computed(() => {
	// 没有数据就返回空数组,防止等待请求时报错
	if (categories.value.length === 0) return []
	// 查找当前的激活一级分类
	const current = categories.value.find(item => item.id === activeId.value)
	// 返回这个激活分类的子分类数组
	return current.children
})
```

```html
<!-- 分类左侧 -->
<view
@click="activeId = item.id"
:class="{ active: item.id === activeId }"
v-for="item in categories"
:key="item.id"
>		
{{ item.name }}
</view>
```
## 五、小程序的登录功能

无论哪种登录方式，整体逻辑：
1. 用户先授权允许小程序客户端获取到当前用户的信息，eg: 邮箱、手机号等等
2. 客户端将用户信息发送到微信服务器加密后返回
3. 客户端再通过调用微信的登录接口`wx.login`获取临时通行码`code`
4. 此时会把用户的加密信息和临时通行码发请求提供给后端，后端会把这些数据以及小程序所属的appid一起发送给微信进行验证，如果小程序验证该appid有权调用登录接口，就返回该用户的会话密钥（session_key）、微信开放平台账号下的唯一标识（unionid）和用户在当前小程序的唯一标识（openid）等`token`，后端保存用户的数据并返回客户端`token`，供登录后的一系列鉴权使用

代码实现步骤： 
1. 登录方式：手机号快速验证，使用button组件：
	- 固定属性即值  `open-type="getPhoneNumber"`，即可调出微信获取电话的询问
	- 固定事件监听 `@getphonenumber`，事件触发会返回事件参数，里面有用户的电话等加密信息
```html
<button open-type="getPhoneNumber" @getphonenumber="getPhoneByWeixin">
```

2. 接收参数，获取用户的加密信息，其中包括加密信息本体`encryptedData`，以及解密算法向量`iv`，两个配合才能解译密文。目的：在微信验证用户与小程序的权限前，公司无法获取到用户的信息
3. 调用内置API`uni.login()`，获取登录的临时通行码`code`
4. 将以上三个数据都放在参数里，发给后端（公司服务器）的登录接口
```js
const getPhoneByWeixin = async (ev) => {
	// 获取用户的加密信息
    const {encryptedData, iv} = ev.detail
    // 获取code
    const {code} = await uni.login()
    const res = await loginReal({
        encryptedData,
        iv,
        code
    })
}
```

5. 如果微信权限验证通过，会告知公司后端用户的相关信息和`token`，后端就可以储存用户信息，并给客户端提供`token`

## 六、小程序的登录流程

概述：
- 电商小程序，所以商品等浏览页面不需要登录，但添加购物车，收藏，支付等操作，以及进入我的页面都需要登录

进入的路径：
- 没有设置在tabBar或者声明式导航，而是使用了编程式导航跳转，在需要权限的页面进行校验是否登录，没有的话就直接跳转至登录页

数据的储存位置：
- 使用Pinia状态管理库，专门创建了一个`user.js`文件来存放用户信息仓库
- 在仓库内，定义了一个变量来存储用户的信息`profile`，声明了一个获取用户数据的登录函数`login()`，并且在该函数内将用户信息储存在`profile`里
- 在需要权限的页面，引入该仓库，获取`profile`变量，如果变量为空，则条件渲染页面或者直接跳转至登录页面
- 在登录页面，引入该仓库，获取`login()`函数，调用进行登录