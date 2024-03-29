## 一、`main.js`入口文件的导入与挂载方式不同

**vue2**：我们可以使用 **pototype (原型)** 的形式去进行操作，引入的是**构造函数**
- 默认导入`Vue`
- 有两种挂载方式：
	- 先`new Vue`实例化一个`Vue`
		1. 通过在这个实例中的`el`属性给`'#app'`进行挂载
		2. 或者在这个实例后面`.$mount('#app')`进行挂载

```js
import Vue from 'vue'
import App from './App'
import router from './router' 
import store from './store'

// 方法一：el属性挂载
new Vue({
	el: '#app',
	router, 
	store,
	render: h => h(App)
})

// 方法二：$mount挂载
new Vue({
	router, 
	store,
	render: (h) => h(App),
}).$mount('#app')
```


**vue3**：vue3中需要使用**结构**的形式进行操作，引入的是**工厂函数**
- 按需导入`createApp`
- 给`createApp`函数传入`App`，再`.mount('#app')`进行挂载

```js
import { createApp } from 'vue'
import App from './App.vue'
import router from './router' 
import store from './store'

const app = createApp(App)
app.use(store).use(router).mount('#app')
// 或者更加简洁的写法
app.use(store)
app.use(router)
app.mount('#app')
```

#构造函数与工厂函数  👉🏻 [[Terms#构造函数与工厂函数 | 构造函数与工厂函数]]


## 二、响应式原理api不同
- Vue2响应式原理采用的是**defineProperty**；
- 而vue3通过**proxy**代理目标对象；
- 这两者前者是**修改对象属性的权限标签**，后者是**代理整个对象**。性能上proxy会更加优秀。

例如，在使用Object.defineProperty()时，要为每一个属性都添加getter和setter，但是在使用Proxy时，只需要添加一个代理就可以监听所有的属性。

另外，Proxy还有一些额外的特性，例如可以拦截对象的操作，从而实现一些高级的功能。

## 三、diff算法/渲染算法优化
- Vue3优化了diff算法。不再像vue2那样比对所有dom，而采用了block tree的做法。
- 此外重新渲染的算法里也做了改进，利用了闭包来进行缓存。这使得vue3的速度比vue2快了6倍。
## 四、选项式API → 组合式API

- 旧的选项型API在代码里分割了不同的**属性**（properties）：data，computed，methods，等等。
- 新的合成型API能让我们用**方法/函数**（function）来分割。定义数据和声明函数可以按照功能写在一起，无需上下翻找了。

**vue2**：选项式API （Options API）
- 在vue2中在`data(){}`中定义数据变量 ，在`methods:{}`中定义方法

**vue3**：组合式API （Composition API）
- 组合式 API 是一系列 API 的集合，使我们可以使用函数而不是声明选项的方式写 Vue 组件
- `setup()` 钩子是在组件中使用组合式 API 的入口，vue3的所有数据与方法都定义在setup()方法里，最后把要用到的数据和函数都return出去
- `setup()` 自身并没有对组件实例的访问权，即在 `setup()` 中访问 `this` 会是 `undefined`。你可以在选项式 API 中访问组合式 API 暴露的值，但反过来则不行

```js
// ref 就是一个组合式API  
import { ref } from 'vue';
export default {
  setup () {
    const show = ref(true)
    const toggle = () => {
      show.value = !show.value
    }
    // 计数器
    const count = ref(0)
    const increment = () => {
      count.value ++
    }
    // 返回c
    return { show, toggle, count, increment }
  }
};
```


语法糖：setup，写在`script`标签内
- 对于结合 #单文件组件 使用的组合式 API，推荐通过 `<script setup>`
- 省略`export default`和`return`
```js
<script setup>
	import { ref } from 'vue';
	const show = ref(true)
	const toggle = () => {
		show.value = !show.value
	}
	// 计数器
	const count = ref(0)
	const increment = () => {
		count.value ++
	}
</script>
```

## 五、生命周期钩子函数不同

**Vue2 & Vue3**

| **Options API** | **Composition API** |
| :-------------- | :------------------ |
| beforeCreate    | 不需要，直接把代码写在setup()中 |
| created         | 不需要，直接把代码写在setup()中 |
| beforeMount     | onBeforeMount       |
| mounted         | onMounted           |
| beforeUpdate    | onBeforeUpdate      |
| updated         | onUpdated           |
| beforeDestroy   | onBeforeUnmount💥   |
| destroyed       | onUnmounted💥       |
| + activated     | onActivated         |
| + deactivated   | onDeactivated       |
| + errorCaptured | onErrorCaptured     |

- 取消了创建阶段的两个钩子，因为setup()函数触发时机为刚创建时，可以替代
- 销毁阶段钩子改名：destroy → unmount
- 其他的直接加on，小驼峰


**Vue3新增生命周期**

| **生命周期钩子** | **触发时机**                                                  |
|:---------------- |:------------------------------------------------------------- |
| onRenderTracked  | 注册一个调试钩子，当组件渲染过程中追踪到响应式依赖时调用;</br>这个钩子仅在开发模式下可用，且在服务器端渲染期间不会被调用</br>  |
| onRenderTriggered| 注册一个调试钩子，当响应式依赖的变更触发了组件渲染时调用;</br>这个钩子仅在开发模式下可用，且在服务器端渲染期间不会被调用 |
| onServerPrefetch | 注册一个异步函数，在组件实例在服务器上被渲染之前调用                   |

## 六、组件通信方式不同

[[In2. Vue3组件通信方式]]

## 七、双向绑定原理不同

[[In4. v-model 的原理]]
