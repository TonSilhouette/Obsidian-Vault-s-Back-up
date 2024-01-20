
**使用方式**：
- `vite`构建项目时选择语言为`TypeScript`
- `script` 加上 `lang="ts"`
```xml
<script setup lang="ts"></script>
```

## 一、为组件的 props 标注类型

### 1.基本使用
```ts
// 原Vue3的JS写法
const props = defineProps({
	money: {
		type: Number,
		required: true
	},
	car: {
		type: String,
		required: false,
		default: '宝马车'
	}
})

// 泛型函数写法 👏🏼
const props = defineProps<{
	money: number
	car?: string //不加可选属性`?`，则限制必传（默认必传，无需`required`）
}>()

// 接口+泛型函数写法 👏🏼
interface Props {
	money: number
	car?: string
}
const props = defineProps<Props>()
```

### 2.设置默认值
语法：`withDefaults(props类型定义, 默认值对象)`

```ts
// 写法一：不声明类型直接写
const props = withDefaults(defineProps<{
	money: number
	car?: string // 只有可选属性可以设置默认值
}>(),{
  car: '宝马车'
})

// 写法二：使用接口声明对象类型
interface Props {
	money: number
	car?: string // 只有可选属性可以设置默认值
}
const props = withDefaults(defineProps<Props>(),{
	car: '宝马车' 
})
```

#### 响应式 props 解构
**作用**：弥补`defineProps`的两个缺点
1. 和 `.value` 类似，为了保持响应性，你始终需要以 `props.x` 的方式访问这些 prop。这意味着你不能够解构 `defineProps` 的返回值，因为得到的变量将失去响应式。
2. 当使用**基于类型的 props 的声明**（即：TS的接口声明）时，无法很方便地声明这些 prop 的默认值。即使有 `withDefaults()` 这个 API，使用起来仍然很笨拙。

**语法**：`defineProps` 与解构一起使用
```ts
interface Props {
	money: number
	car?: string
}
const { money, car = "宝马车" } = defineProps<Props>();
```

**显式启用**:
```js
// vite.config.js
export default {
  plugins: [
    vue({
      reactivityTransform: true
    })
  ]
}
```

## 二、为组件的 emits 标注类型

### 基本使用
```ts
// 原Vue3的JS写法
const emit = defineEmits(['changeMoney', 'changeCar'])

// 泛型函数+函数调用签名写法 👏🏼
const emit = defineEmits<{ 
	(name: 'changeMoney', value: number): void // 参数列表：键值对形式
	(name: 'changeCar', value: string): void // 返回类型：一定是void
}>()

// 接口+泛型函数+调用签名写法 👏🏼
interface Emits { 
	(name: 'changeMoney', value: number): void 
	(name: 'changeCar', value: string): void 
} 
const emit = defineEmits<Emits>()
```

####  函数的调用签名 (Call Signatures)
目的：更加精确地限制函数的参数，实现统一声明但一一对应的效果
语法：`{ (参数列表):返回类型 }`
要点：
- 参数列表：键值对类型
	- 自定义事件名：一定是字面量类型，才有一一对应效果
	- 参数：常规类型
- 返回类型：一定是`void`
```ts
// 普通函数的写法
type MyFn = (name: "changeNum" | "changeStr", value: number | string ) => void
const fn: MyFn = (name, value) => {};
// 均不报错
fn("changeNum", "1");
fn("changeMsg", 1);
fn("changeNum", "1");
fn("changeMsg", 1);

// 函数的调用签名写法
// ⭐️函数类型表达式语法不允许声明属性，所以要使用对象类型（函数也是对象）
interface MyFn { 
	(name: "changeNum", value: number): void; 
	(name: "changeStr", value: string): void; 
}
const fn: MyFn = (name, value) => {};
fn("changeNum", "1"); // ❌
fn("changeMsg", 1); // ❌
fn("changeNum", "1"); // ✅ 
fn("changeMsg", 1); // ✅
```

## 三、为响应式数据标注类型

### 1.ref()
```ts
// 简单数据, 直接使用类型推导
const money = ref(100)
const car = ref('五菱')

// 复杂数据, 使用泛型函数，传入类型别名作为泛型参数
// 作用：通过限制函数ref()的参数类型，来限制其类型推断，其返回值又会限制变量的类型
type HistoryType = {count: number, time: string}[]
const list = ref<HistoryType>([])
list.value.push({
    count: 100,
    time: '2023-01-01'
})
```

### 2.reactive()
```ts
// 官方建议reactive()直接对变量使用类型别名, 不用泛型
type BookType = {
  name: string
  author: string
}
const book: BookType = reactive({
  name: 'js入门',
  author: '张三',
})
```

## 四、为 `computed()` 标注类型

- `computed()`会自动从其计算函数的返回值上推导出类型
- 想特殊指定就使用泛型函数`computed<number>()`

```ts
// 能够推导就直接使用
const myNum = ref(666)
// const myDoubleNum = computed<number>(()=>{
const myDoubleNum = computed(()=>{
  return myNum.value * 2
})
```

## 五、事件处理函数的参数

- 没有类型标注时，`event`参数会隐式地标注为`any`类型，会导致TS报错
- 所以，需要对`event`进行类型断言，显式地进行类型注解
```ts
// `event` 隐式地标注为 `any` 类型，如何指定：event 类型? 
// 1. @change="handleChange($event)"" 查看$event类型 
// 2. 鼠标悬停事件 @change 查看类型 
const handleChange = (event: Event) => { 
	// `event.target` 是 `EventTarget | null` 类型，如何指定具体类型？ 
	// document.querySelector('input') 查看返回值类型
	console.log((event.target as HTMLInputElement).value) 
}
```

## 六、为 provide / inject 标注类型


## 七、为模板引用标注类型

### 1.获取DOM元素
```ts
<script setup lang="ts"> 
import { ref, onMounted } from 'vue' 
// 类型注解使用泛型函数，参数为 本身的类型 联合 null类型，传入默认值null
const el = ref<HTMLInputElement | null>(null) 
// 大前提：写在挂载后钩子内
onMounted(() => { 
  // 对于可能有, 也可能为空的内容, 类型安全控制有三种办法:
  // 1. 可选链
  el.value?.focus()
  // 2. if条件判断(又叫类型守卫)
  if (el.value) {
    el.value.focus()
  }
  // 3. 非空断言(断言前面的数据一定存在，即不为null或undefined)
  el.value!.focus()
}) 
</script> 
<template> 
	<input ref="el" /> 
</template>
```

### 2.获取组件实例

