## 一、概念

**权限**是对特定资源的访问许可
**权限控制**，就是确保用户只能访问到被分配的资源

## 二、权限控制分类（前端）

前端权限归根结底是**请求的发起权**，请求的发起主要由下面两种形式触发

- 页面加载触发
	1. 路由权限
	2. 菜单权限
	3. 接口权限（后端去做）
- 页面上的按钮点击触发
	4. 按钮权限

## 三、控制方法

### 1. 路由权限控制

**方法一：导航前置守卫 + 路由元信息**

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

**方法二：导航前置守卫 + 路由白名单**

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

1. 在路由文件中`router/index.js`，路由表中只放不需要权限认证的静态路由`constantRoutes`
2. 登录时，从登录页跳转至首页，触发导航守卫，发请求确认该用户权限，将权限数组保存在vuex中
3. 在vuex中，在全部权限的数组中过滤出用户有的权限（不直接使用用户权限数组是因为有多层）。路由文件引入该权限数组，用`addRoutes()`方法，将动态路由加入路由表中

```js file:router/index.js
const createRouter = () => new Router({
  // mode: 'history', // require service support
  scrollBehavior: () => ({ y: 0 }),
  routes: [...constantRoutes]
})

// 加到路由里面 -- 这就是给路由加路由规则
router.addRoutes(routes)
// 更新路由表 -- 更新后菜单栏才会刷新动态路由的菜单
router.options.routes = [...constantRoutes, ...routes]
```

### 4. 按钮权限控制

1. 使用`v-if`调用仓库中数据，包含权限则显示，不包含则不显示
2. 调用仓库查找权限可以封装成函数，更简洁
3. 很多页面需要进行按钮权限判断，所以使用`mixin`做全局混入，将该函数混入所有组件的`methods`中

- 原写法
```html
<el-button
	v-if="$store.getters.userInfo.roles.points.includes('EMP_EXPORT')"
></el-button>
```

- 封装函数，放在methods中
```js
methods: {
	checkPoint(key) {
		return this.$store.getters.userInfo.roles.points.includes(key)
	}
}
```

- 新写法-简洁
```html
<el-button v-if="checkPoint('POINT_TRUE')">转正</el-button>
```

- 在`main.js`全局混入该函数
```js
import Vue from 'vue'
const obj = {
  methods: {
    checkPoint(key) {
      return this.$store.getters.userInfo.roles.points.includes(key)
    }
  }
}
Vue.mixin(obj)
```

