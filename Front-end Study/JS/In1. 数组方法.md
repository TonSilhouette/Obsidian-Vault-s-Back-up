## 一、添加 / 移除数组元素

### 1-4. push, pop, shift, unshift
*🟠会改变原数组*
- `arr.push(...items)` —— 从尾端添加元素
- `arr.pop()` —— 从尾端提取元素
- `arr.shift()` —— 从首端提取元素
- `arr.unshift(...items)` —— 从首端添加元素

### 5. splice 
*🟠会改变原数组*
可以灵活地在数组中插入和删除元素
```js
arr.splice(start,deleteCount,item1,...,itemN)
```

- `start` 必须
- `deleteCount` 可选，0则不删除
- `item` 可选，不写则不添加
## 二、复制 / 合并数组元素

### 1. slice
*🟢不改变原数组*
```js
arr.slice(start,end)
```

- 它会返回一个新数组，将复制所有从索引 `start` 到 `end`（不包括 `end`）的数组项
- `start` 和 `end` 都可以是负数，在这种情况下，从末尾计算索引
### 2. concat
*🟢不改变原数组*
```js
let arr = [1,2];
console.log(arr.concat([3,4])) // 1,2,3,4
console.log(arr.concat([3,4],[5,6])) // 1,2,3,4,5,6
```

- 创建一个新数组，其中包含来自于其他数组和其他项的值
- 它接受任意数量的参数 —— 数组或值都可以
- 多个数组用逗号隔开

## 三、遍历数组

### 1. for
```js
for(let i = 0; i < array.length; i++) { 
	console.log(Array[i]) 
}
```

### 2. forEach
```js
array.forEach(function (item, index) {
	// ... do something with item
})
```

#### for 与 forEach 区别
1. **适用对象：** for循环适用于任何需要重复执行指定次数的情况，而foreach循环专门用于遍历集合类型的数据，更加简洁易读
2. **循环变量：** for循环需要在外部显式声明循环变量，并在循环体内进行更新操作。而foreach循环则不需要显式声明循环变量，直接将集合中的元素赋值给一个临时变量
3. **索引访问：** for循环可以通过索引访问数组或列表中的元素，因为循环变量i可以作为索引。而foreach循环只能逐个访问集合中的元素，不能直接获取索引
4. **遍历方式：** for循环可以根据需要自由设置*循环条件*和*迭代步长*，可以实现倒序遍历等复杂遍历方式。而foreach循环只能顺序遍历集合中的元素

### 3. map
```js
array.map(function (item, index) { 
	// ... do something with item
})
```

#### for 与 map 区别
可以返回一个数组，以配合join渲染页面，如：
```js
ul.innerHTML = arr.map(function (v, i) {     
	return `<li>${v.name}</li>` 
}).join('')
```

## 三、遍历数组
