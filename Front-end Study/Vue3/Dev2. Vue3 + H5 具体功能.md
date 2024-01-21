## 一、登录功能

技术栈：Vu3, TS, Pinia, Vant

1. 登录方式：手机号验证码登录，账号密码登录
2. 布局方式：
	- 顶部有一个封装的`NavBar`组件，左侧为返回箭头，右侧为注册键
	- 主体部分为两个输入框和一个登录键，使用了Vant的组件 (`van-form`, `van-field`, `van-button`)
		- `van-button`设置属性`native-type="submit"`即可赋予按钮原生的提交行为
		- `van-form`绑定事件`@submit="xx"`，即可在表单全部校验通过后才执行提交
	- 手机号登录：手机号、验证码+发送验证码的按钮
	- 账号登录：账号、密码+一个眼睛图标控制密码明文或密文切换
3. 两种登录方式的切换：
	- 声明一个布尔值，如`isPsw`
	- 将`isPsw`绑定给需要切换的元素的`v-show`属性，加点击事件给`isPsw`取反来切换元素显示
	- 登录按钮也要做判断，根据登录方式的不同发不同的请求
4. 封装请求函数与接口：
	- 封装请求函数：基于axios进行封装，设置了请求拦截与响应拦截
	- 封装登录接口：使用封装的请求函数，参数在调用时传入
5. 发送请求：调用登录接口，传入双向绑定的表单内容
6. 请求后得到的用户信息与`token`都需要持久化储存，用来后续的校验登录
	- 我使用了`Pinia`，在`main.ts`里导入，挂载之后，创建了一个库`user.ts`
	- 创建使用仓库的函数：
		- 声明了一个存放数据的对象变量
		- 定义了一个赋值该变量的函数，拿到数据后调用
		- 定义了一个清空变量的函数，退出登录时调用
		- 最后都暴露了出去
	- 该仓库还使用了持久化插件，刷新后数据还在（`defineStore`的第三个参数）
7. 在登录函数内，将拿到的数据放入仓库

## 二、嵌套路由

**什么情况下使用**
嵌套路由在vue的项目中几乎是必须的。因为Vue倡导单页面应用开发，使用路由就可以让页面跳转路径但却不重新加载全部的页面，只是重新加载切换掉的组件，让交互变得更丝滑。页面与组件之间的嵌套就需要路由之间的嵌套。

**怎么配置**
1. 引入：Vue3中的路由需要引入组合式API：
	- `createRouter()`创建路由的函数
	- `createWebHistory()` / `createWebHashHistory()` 历史模式/哈希模式
2. 使用：`createRouter()`内设置
```ts
const router = createRouter({
	history: createWebHistory(import.meta.env.BASE_URL),
	routes: [
		// 首页布局容器
		{
			path: '/',
			// 如果只有一级路由的url, 希望重定向到首页
			redirect: '/home',
			// 一级路由容器
			component: () => import('@/views/Layout/index.vue'),
			meta: { 信息名: 数据 }, // 路由元信息
			children: [
				// 二级页面
				{
					path: 'home',
					component: () => import('@/views/Home/index.vue')
				}
			]
		},
		// 登录页
		{ 
			path: '/login',
			component: () => import('@/views/Login/index.vue') 
		}
	]
})
```

## 三、底部导航切换高亮

方案一：vant
方案二：原生