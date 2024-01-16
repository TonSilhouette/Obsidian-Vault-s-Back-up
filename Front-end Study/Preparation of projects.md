## 从头搭建
### 1. 自定义创建项目
- `create new project-name`
- manually：
		- Babel 语法降级、按需导入
		- Router 路由
		- Veux
		- Css Pre-processor预处理 less/css/stylus
		- ESLint 语法规范
- vue: 2.x
- do not use history mode
- pre-processor: less
- ESLint : standard config
- Lint on save
- dedicated config files 独立配置文件
- do not save this preset
### 2. 配置ESLint
- 在settings.json中放入以下代码
````json
  "eslint.run": "onType",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
````
### 3. 删除、修改脚手架默认配置
- 删除`views`文件夹里默认的 `homeview.vue`和`aboutview.vue`
- 删除 `components`文件夹里的 `HelloWorld.vue`
- 删除 `assets`文件夹里的 `logo.png`
- 删除 `app.vue`里的默认代码，只留基本结构
- 删除 `router`里的部分默认代码和默认的路由规则
### 4. 下包/插件与配置
==`eslint-plugin-vue`版本过高的报错==
- 解决方法：
```bash
# 先卸载
npm un eslint-plugin-vue
# 然后下载指定版本
npm i eslint-plugin-vue@7.0.0 -D
```
1. vant
```bash
	npm i vant@latest-v2 -S
```
2. bable
```bash
	npm i babel-plugin-import -D
```
- to `babel.config.js`
```js
   plugins: [
    ['import', {
      libraryName: 'vant',
      libraryDirectory: 'es',
      style: true
    }, 'vant']
  ]
```
3.  postcss - px - to - viewport
```bash
	npm i postcss-px-to-viewport
```
- add `postcss.config.js` （package.json同级）
```js
  module.exports = {
  plugins: {
    'postcss-px-to-viewport': {
      viewportWidth: 375, // 这个值根据设计稿得出
    },
  },
};
```
### 5. 配置文件相关文件
#### `src`文件夹
##### `styles`样式文件夹
- `base.less`文件，放基本样式
	```less
	* {
    margin: 0;
    padding: 0;
    font-size: 14px;
	}
	```
- 在`main.js`中做导入
	```js
	import '@/styles/base.less'
	```

##### `api`接口文件夹
- `接口分类.js`

##### `assets`静态资源文件夹
- 需要用的静态资源，如：图片、字体图标

##### `components`组件文件夹
- 放全局组件

##### `router`路由文件夹
- `index.js`文件
```js
// 给Vue安装路由插件 
import Vue from 'vue'
import VueRouter from 'vue-router' 
Vue.use(VueRouter)
// 导入页面组件，@代表src目录 
import HomeView from '@/views/大驼峰组件名.vue' 
// 创建路由规则 --> 设置哪个路径对应哪个组件 
const routes = [{  
	// path设置路径    
	path: '/a',    
	// 给这个路由起个名字    
	name: 'home',    
	// 路径对应的组件    
	component: HomeView 
}]
```

##### `store`vuex文件夹
- `index.js`文件
```js
// 给Vue安装vuex插件 
import Vue from 'vue'
import Vuex from 'vuex'
Vue.use(Vuex)
```

##### `utils`文件夹
- `vant.js`文件，放自动按需导入的vant代码
- `request.js`文件，封装接口
	1. 多基地址
	```js
	//导入axios，之后的文件不需要再导入
	axios import axios from 'axios' 
	//创建一个新的axios对象request，同时设置基地址 
	const request = axios.create({ baseURL: 'xxx' })
	```
	2. 请求与响应拦截
	```js
	// 添加请求拦截器
	request.interceptors.request.use(function (config) {
	    // 在发送请求之前做些什么
	    return config;
     }, function (error) {
	    // 对请求错误做些什么
	    return Promise.reject(error);
     }); 
   // 添加响应拦截器
   request.interceptors.response.use(function (response) {
	    // 数据剥离
	    return response.data;
     }, function (error) {
	    // 超出 2xx 范围的状态码都会触发该函数。
	    // 对响应错误做点什么
	    return Promise.reject(error);
     });
     // 暴露对象
	export default request
	```

## 基于模板搭建
