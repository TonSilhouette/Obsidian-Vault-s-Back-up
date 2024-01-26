## 一、字典概述

- 字典是一种以**键-值对**形式存储数据的数据结构
- 字典中的**键**，是值在字典中的**索引**
- 对于 js 来说，字典类（Dictionary）的基础是 **Array 类**， js 中的 Array 既是一个**数组**，同时也是一个**字典**

## 二、使用步骤

`utils/constant/common` 建立字典
```js
export default {
// 运输任务状态
taskStatus: [
	{
		id: 1,
		value: '待执行'
	},
	{
		id: 2,
		value: '进行中'
	},
	{
		id: 3,
		value: '待确认'
	},
	{
		id: 4,
		value: '已完成'
	},
	{
		id: 5,
		value: '已取消'
	}
],
...
}
```

在需要使用的组件/页面中引入字典
```js
import dic from '@/utils/constant/common'
```

在过滤器中传入form中的运输任务状态，用find滤出该id的value
```js
filters: {
	// 运输任务状态
	formatTaskStatus(val) {
	const obj = dic.taskStatus.find(v => +v.id === +val) // 注意类型转换
	return obj.value
	}
}
```

把el-table-column改为双标签，使用插槽，在插值语法中使用过滤器
```vue
<el-table-column
	prop="status"
	label="运输任务状态"
	width="150"
>
	<template v-slot="{row}">
	{{ row.status | formatState }}
	</template>
</el-table-column>
```