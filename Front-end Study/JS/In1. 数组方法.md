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
## 二、复制 / 合并 数组元素

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
*🟠会改变原数组*
```js
for(let i = 0; i < array.length; i++) { 
	console.log(Array[i]) 
}
```

### 2. forEach
*🟠会改变原数组*
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
*🟢不改变原数组*
```js
array.map(function (item, index) { 
	// ... do something with item
})
```

#### for 与 map 区别
`arr.map`可以返回一个数组，以配合join渲染页面，如：
```js
ul.innerHTML = arr.map(function (v, i) {     
	return `<li>${v.name}</li>` 
}).join('')
```

## 四、在数组中搜索

### 1. indexOf, lastIndexOf
*🟢不改变原数组*
- `arr.indexOf(item, from)` —— 从索引 `from` 开始搜索 `item`，如果找到则返回索引，否则返回 `-1`
- `arr.lastIndexOf(item, from)` ——从右往左查找
- 一般只传入`item`，从头开始搜索

### 2. includes
*🟢不改变原数组*
- `arr.includes(item, from)` —— 从索引 `from` 开始搜索 `item`，如果找到则返回 `true`，否则返回 `false`

### 3. find
*🟢不改变原数组*
- `arr.find`会返回使函数返回 `true` 的第一个元素
- `arr.find`会依次对数组中的每个元素调用该函数
- 返回 `true`，则搜索停止，并返回 `item`
- 如果没有搜索到，则返回 `undefined`
```js
let result = arr.find(function(item,index,array) {
	// 如果返回 true，则返回 item 并停止迭代
	// 对于假值（false）的情况，则返回 undefined
	// item : 元素
	// index : 索引
	// array : 数组本身
})
```

- 常用于查找对象数组
```js
let users =[
	{id : 1, name : "John"},
	{id : 2, name : "Pete"}
];
let user = users.find(item => item.id == 1);
alert(user.name); // John
```

### 4. filter
*🟢不改变原数组*
`arr.filter`会返回所有匹配元素（使函数返回true）组成的数组
```js
let users =[
	{id : 1, name : "John"},
	{id : 2, name : "Pete"}
];
let user = users.filter(item => item.id < 3);
alert(user.length); // 2
```

## 五、数组排序

### 1. sort
*🟠会改变原数组*
`arr.sort`方法对数组进行**原位/就地**排序，更改元素的顺序

语法：
```js
sort()
sort(compareFn)
```

`compareFn`：
定义排序顺序的函数。返回值应该是一个数字，其符号表示两个元素的相对顺序：如果 `a` 小于 `b`，返回值为负数，如果 `a` 大于 `b`，返回值为正数，如果两个元素相等，返回值为 `0`。`NaN` 被视为 `0`。该函数使用以下参数调用：
1. `a`
	第一个用于比较的元素。不会是 `undefined`。
2. `b`
	第二个用于比较的元素。不会是 `undefined`。

如果省略该函数，数组元素会**被转换为字符串**，然后根据每个字符的 Unicode 码位值进行排序

```js
arr.sort((a,b) => a-b) // 从小到大
arr.sort((a,b) => b-a) // 从大到小
```

### 2. reverse
*🟠会改变原数组*
`arr.reverse`方法用于颠倒 `arr` 中元素的顺序

```js
let arr = [1,2,3,4,5]
arr.reverse
alert( arr ) // [5,4,3,2,1]
```
