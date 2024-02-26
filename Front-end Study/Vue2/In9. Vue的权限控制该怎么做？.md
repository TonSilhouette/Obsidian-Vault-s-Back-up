## 一、概念

**权限**是对特定资源的访问许可
**权限控制**，就是确保用户只能访问到被分配的资源

## 二、权限控制分类（前端）

前端权限归根结底是**请求的发起权**，请求的发起主要由下面两种形式触发

- 页面加载触发
	1. 路由权限
	2. 菜单权限
	3. 接口权限
- 页面上的按钮点击触发
	4. 按钮权限

## 三、控制方法

### 1. 路由权限控制

**方法一：导航前置守卫+路由元信息**

适用：需要权限的页面少，如：新闻头条

```js file:src/permission.js hl:9
// 导入路由，仓库和通知
import router from './router'
import store from '@/store' 
import { Toast } from 'vant' 

// 全局前置守卫 
router.beforeEach((to, from, next) => {   
	// 判断要去的(to)是不是需要登录的页面   
	if (to.meta.needLogin) {     
		// 代表是需要登录的页面     
		// 再判断有没有token    
		if (store.state.user.tokenObj.token) {       
			// 登录了，直接放行       
			next()   
		} else {       
			// 没登录，打回登录页       
			Toast('请先登录')       
			// next('/login') 这是直接给路径跳转的方法 
			next({ 
				name: 'login', // 这是用路由名跳转 
				query: { 
					back: to.path // 路由传参：来时的路径 
				}   
			}) 
	} else {    
		// 不需要登录的页面，直接放行    
		next() 
	} 
})
```

**方法二：导航前置守卫+路由白名单**

适用：需要权限的页面多，如：后台管理

```js file:src/permission.js hl:12
// 导入路由，仓库和通知
import router from './router'
import store from './store'
import { Message } from 'element-ui'
​
// 白名单 - 网页有些页面需要登录，有些页面不需要登录就能访问（例如登录页，404页面）
// 属于白名单里的直接放行，不属于的才判断有没有登录
const whiteList = ['/login', '/404']
​
router.beforeEach(async(to, from, next) => {
  // 判断去的是不是白名单页面
  if (whiteList.includes(to.path)) {
    // 在里面就直接放行
    next()
  } else {
    // 不在白名单里 - 有没有token
    // store.state.user.token
    if (store.getters.token) { // 用计算属性简化了
      next()
    } else {
      // 打回登录页，先弹出提示
      Message.warning('请先登录')
      next('/login')
    }
  }
})
```

### 2. 菜单权限控制

登录时，将权限数组保存在vuex中，

```js file:router/index.js
const createRouter = () => new Router({
  // mode: 'history', // require service support
  scrollBehavior: () => ({ y: 0 }),
  routes: [...constantRoutes]
})

// 加到路由里面 -- 这就是给路由加路由规则
router.addRoutes(routes)
// 更新路由表 -- 更新后菜单才有
router.options.routes = [...constantRoutes, ...routes]
```
