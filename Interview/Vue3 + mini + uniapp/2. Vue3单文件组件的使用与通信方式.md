## 一、基础使用

- 组件的创建、引用与注册和Vue2相同
- 💥Vue3中的组件==无需注册==即可使用
## 二、组件传值

### 1. 父传子
#### 子要
```js
const props = defineProps({
	parentsMoney: {
		type: Number,
		required: true,
		//限制了必传就不需要default
	},
	parentsCar: {
		type: String,
		required: true,
	}
});
console.log(props.parentsMoney);

```

```html 
<template>
	收款 {{ parentsMoney }} 元
	<br />
	跑车：{{ parentsCar }}
</template>
```

- 子组件内声明`defineProps`函数 ← Vue2：`props`对象🤡
- `defineProps`内的对象名即为父传过来的数据名，可以直接在`template`内使用
- 如果想在`script`里对父传子的数据进行操作：
	- 可以在`defineProps`前定义一个对象进行接收，一般名为`props`
	- 就可以通过`props.xx`来进行使用了

#### 父给
```html
<children-com
:parentsMoney="money"
:parentsCar="carList"
></children-com>
```

- 直接在子组件内用属性的方式赋值，也可以绑定属性传数据

### 2. 子传父
#### 子通知
```html
<button @click="informParent">点我就送跑车</button>
```

```js
const emit = defineEmits(['changeCar']);
const informParent = () => {
	emit("changeCar"，payload);
};
```

- 定义一个emit函数，给它一个`defineEmits`方法，该方法的参数可以接收自定义事件名，如果有多个事件名可以放在数组里传入
- 未传入`defineEmits`作参数的事件名无法发送
- 在需要子传父的事件内，调用`emit`函数并传入事件名，即可向父组件发送该自定义事件
- 子组件传参可以用逗号隔开放在事件名后

#### 父接收
```html
<children-com @changeCar="handleCar"></children-com>
```

```js
const handleCar = () => {
	const carNames = ["保时捷", "法拉利", "兰博基尼", "玛莎拉蒂"];
	const randomIndex = Math.floor(Math.random() * carNames.length);
	carList.value = carNames[randomIndex];
};
```

- 在子组件标签内监听自定义事件，并触发回调函数进行处理；如果只有一句代码也可以直接写在事件监听后
- 若子组件传参，可以通过回调函数的形参进行接收，或者在一句处理代码中用$event接收

#### 用`ref`（模板引用）快速子传父

- 注册方式与Vue2一致：子组件标签，属性ref

```html title:"父组件-template"
<children-com ref="children"></children-com>
<button @click="getChildren">点我触发子组件的打印</button>
```

```js title:"父组件-script"
import { ref } from "vue";
const children = ref();
const getChildren = () => {
	children.value.sayHello();
};
```

```js title:"子组件-script"
const sayHello = () => {
	console.log("子组件说你好");
};
defineExpose({ sayHello });
```

- 获取子组件内数据与函数的方式略有不同：
	1. 使用了 `<script setup>` 的组件是**默认私有**的：外部无法访问到该组件中的任何东西，所以需要在子组件内使用`defineExpose`宏进行显式暴露：数据与函数都放在一个对象内暴露
	2. 父组件需要一个声明响应式变量，一般使用`ref`设置响应式，记得用的时候引入`ref`
	3. 该变量需要与子组件注册的`ref`名字相同，`ref`内先给空值
		   `const myComp = ref()`
	4. 此时父组件就可以使用`子组件注册名.value.子组件暴露的数据或函数名`获取到子组件的内容了

### 3. 兄弟组件传值
- 利用父组件塔桥传递 - `defineProps`&`defineProps`
- Vue 状态管理库 - Pinia

### 4. 祖先后代组件传值
#### 祖先提供依赖-provide
```js
import {ref, provide} from 'vue'
const count = ref(1)
provide('count', count)
```

- 祖先需要引入`provide`
- 在`setup()`内调用`provide`函数传入两个参数：
	- 第一个是**注入名**，后代组件会用注入名来查找期望注入的值
	- 第二个参数是**祖先提供的值**，该值可以是任何类型，也可以是响应式

#### 后代注入依赖-inject
```js
import { inject } from 'vue';
const count = inject('count')
```

```html
<div class="box">
	<h3>这是第三层的子组件</h3>
	<strong>{{ count }}</strong>
</div>
```

- 后代组件需要引入`provide`
- 需要声明一个`变量 = inject(注入名)`来接收数据，该变量此时就是上方传过来的数据了
- 建议：
	1. 提供依赖一方的**注入名**、**数据名**，与注入依赖方的**变量名**保持一致，用的方便
	2. 当提供 / 注入**响应式**的数据时，尽可能将对响应式状态的变更都保持在供给方组件中。这样可以确保所提供状态的声明和变更操作都内聚在同一个组件内，使其更容易维护