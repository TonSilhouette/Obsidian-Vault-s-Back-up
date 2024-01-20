
## 封装的组件

### 顶部导航按钮（Vant）

1. 基本结构和样式确定：左中右
	- 左：左箭头
	- 中：页面标题
	- 右：一些下一步的操作，如注册页就是注册，编辑页就是保存
2. 功能确定：
	- 左侧箭头：
		- 固定存在
		- 点击事件固定是返回上一页
	- 中间文字：
		- 传入才显示
	- 右侧按钮：
		- 传入才显示
		- 点击事件传入
3. 封装组件

```ts file:src/components/NavBar
<template>
	<van-nav-bar
	left-arrow // 固定存在
	fixed 
	placeholder
	:title="title" // 中间文字内容
	:right-text="rightText" // 右侧文字内容
	@click-right="emitParent" // 右侧点击事件内容
	@click-left="goBack" // 左侧点击事件内容
	/>
</template>
```

- 显示文字由父组件传入，一起写在`defineProps()`里，加可选属性
```ts
defineProps<{
	title?: string
	rightText?: string
}>()
```

- 左侧箭头点击事件固定为返回上一页
```ts
const goBack = () => {
	router.back()
}
```

- 右侧点击事件用`emit()`通知父组件，由父组件来自己决定执行什么操作
```ts
const emit = defineEmits<{
	(name: 'clickRight'): void
}>()
const emitParent = () => {
	emit('clickRight')
}
```