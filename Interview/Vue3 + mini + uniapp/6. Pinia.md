Pinia 是 Vue 的专属**状态管理库**，它可以实现跨组件或页面共享状态（数据）

## 一、`main.js`导入与挂载

- 方法一：网页版，需要`mount`
```js hl:2,6,8 
import { createApp } from 'vue'
import { createPinia } from 'pinia'
import App from './App.vue'

const app = createApp(App)
const pinia = createPinia()

app.use(pinia)
app.mount('#app')
```

- 方法二：uniapp，只需要return，小程序无需`mount`，网页uniapp会自动处理
```js hl:2,7,10
import { createSSRApp } from "vue";
import * as Pinia from 'pinia';
import App from "./App.vue";

export function createApp() {
	const app = createSSRApp(App);
	app.use(Pinia.createPinia());
	return {
		app,
		Pinia,
	};
}
```


## 二、定义仓库`Store`

```js file:"src/store/counter.js"
import { defineStore } from "pinia"
import { computed, ref } from "vue"

export const useCounterStore = defineStore("counter", () => {
    // state → 响应式变量
    const count = ref(666)

    // mutations，actions → 普通函数
    const changeCount = () => {
        count.value++
    }
    const asyncChangeCount = () => {
        setTimeout(() => {
            count.value += 3
        }, 1000);
    }

    // modules → 没有这个概念, 因为 defineStore 函数可以多次调用，不重名即可

    // getters → 真的计算属性
    const doubleCount = computed(()=>{
        return count.value * 2
    })

    // 要在外面使用的东西都要返回
    return {
        count,
        changeCount,
        asyncChangeCount,
        doubleCount
    }
})
```

- Store是用`defineStore()`函数定义的
	- 返回值的命名最好以`use`开头，以`Store`结尾
	- `defineStore()`可以接收两个参数
		- 第一个参数是一个独一无二的 id ，是必须传入的，Pinia 用它来连接 store 和 devtools
		- 第二个参数可接受两类值：**Setup() 函数** 或 **Option 对象**
		- 这两类都可以写作一个**箭头函数**
		- 箭头函数内的写法:
			- Option 对象：与 Vue 的选项式 API 类似
				- 对象包函数：`state: () => ({ count: 0 })`
			- Setup() 函数：与 Vue 组合式 API 的`setup()`类似
				- 函数写法：`const count = ref(0)`
				- 需要共享的数据与方法返回出去：`return { count, increment }`

- 箭头函数内（Setup() 函数 ）：
	- state → 响应式变量`ref()`
	- mutations，actions → 普通函数
	- getters → 真的计算属性`computed()`
	- modules → 没有这个概念, 因为 `defineStore()`函数可以多次调用，不重名即可
	- `ref()`与`computed()`需要导入


## 三、使用仓库`Store`

- 在需要使用仓库的组件中引入（以`use`开头，以`Store`结尾的）函数，该函数的返回值即为该仓库实例
- 就可以使用点语法获取仓库内数据与方法了
```js
<script setup>
	import { useCounterStore } from "./store/counter"
	// 可以在组件中的任意位置访问 `store` 变量 ✨
	const store = useCounterStore()
</script>
```

```html
{{ store.count }}
<button @click="store.changeCount">同步变更</button>
<button @click="store.asyncChangeCount">异步变更</button>
```
## 四、`storeToRefs()`保持其响应性

- 为了从 store 中**提取**或者**解构**属性时，让属性仍**保持响应性**，可以使用 `storeToRefs()`
- 该方法会跳过所有的 `action`或非响应式 (不是 ref 或 reactive) 的属性

```js
const { name, doubleCount } = storeToRefs(store)
```