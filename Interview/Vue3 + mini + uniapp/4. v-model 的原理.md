原生表单组件：和Vue2原理一样
1. `v-bind:value` / `:value`给表单元素绑定响应式数据
2. 通过绑定input事件`v-on:input` / `@input`，触发事件获取$event.target.value，然后赋值给当前变量

自定义组件：
```js title:"子组件-script"
const props = defineProps({
  modelValue: Number,
})

const emit = defineEmits(['update:modelValue'])

const handleClick = () => {
  emit('update:modelValue', props.modelValue + 1)
}
```

```html title:"子组件-template"
<template>
    <div class="box">
      这里是子组件 {{ modelValue }}
      <button @click="handleClick">增加</button>
    </div>
</template>
```


```js title:"父组件-script"
import { ref } from 'vue';
import myBox from './components/myBox.vue';
const count = ref()
```

```html title:"父组件-template"
<template>
  <div class="box">
    这里是父组件 {{ count }}
    <myBox v-model="count"/> 
    // :modelValue="count" 
    // @update:model-value="count = $event"
  </div>
</template>
```

子组件内：
- 父传子的数据名需为`modelValue`
- 子传父的事件名需为`update:modelValue`

父组件内：
- 即可使用`v-model=要双向绑定的数据`
- 代码原理：
	- `:model-value="xxx" `
	- `@update:model-value="xxx = $event"`