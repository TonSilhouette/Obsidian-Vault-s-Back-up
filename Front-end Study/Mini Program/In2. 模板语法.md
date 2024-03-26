## 一、声明与绑定数据、事件

### 声明
**数据：**
- data写在`index.js`中的`Page({})`中
- 在`js`中引用data需要使用`this.data.数据名`
- 修改数据后想要渲染在页面，需要在`this.setData({})`内赋值，且是**键值对**的形式，而不是等号赋值
	- 原因：逻辑层与视图层分离，需要手动把逻辑层面的值给到视图层

- setData触发视图层渲染的大致流程是：
	- 在逻辑层调用setData会触发虚拟DOM树的遍历更新
	- 将data中的数据从逻辑层传到视图层
	- 视图层真实DOM的更新及页面渲染
	-  💥注意：数据从逻辑层发送到视图层是异步的，即渲染过程异步；`setData`在设置data数据中是同步的。就是说**改变值是同步的，改变值之后渲染页面是异步的**。

**事件：**
- 与data同级
```js
Page({
	data: {
		num: 0,
	},
	add() {
		this.setData({
			num : +this.data.num + 1
			// 该隐式转换防止双向绑定num后，用户从输入框内修改了值，此时num为字符串
	})
})
```

### 绑定
通过**插值语法**来绑定数据
```html
<input type="text" value="{{num}}" />
```

通过`bind:事件类型`来绑定事件
```html
<button bind:tap="add">+</button>
```

## 二、数据双向绑定

- 数据层与视图层绑定
- 使用`model:value`来实现
```html
<input type="text" model:value="{{num}}"/>
```

## 三、列表渲染

- 使用`wx:for="{{ 数据名称 }}"`来循环遍历列表渲染
	- 直接遍历数据，默认每一项为`item`，下标为`index`
- 使用`wx:key=“对象的属性名”`给列表每一项绑定key，需要是唯一值
	- 不用`item.属性名`,不用写插值表达式
```html
<view class="students"> 
	<view class="item"> 
		<text>序号</text> 
		<text>姓名</text> 
		<text>年龄</text> 
		<text>性别</text> 
	</view> 
	<view class="item" wx:for="{{students}}" wx:key="name"> 
		<text>{{index + 1}}</text> 
		<text>{{item.name}}</text> 
		<text>{{item.age}}</text> 
		<text>{{item.gender}}</text> 
	</view> 
</view>
```

- **简单数据列表渲染**：每一项就是`item`，没有属性
```js
fruitList: ['apple','banana']
```
- 此时`wx:key="*this"`，代表`key`绑定每一项
```html
	<view wx:for="{{fruitList}}" wx:key="*this"> 
		{{index + 1}}: {{item}}
	</view> 
```

- **自定义索引和对象的变量名**：在列表渲染中又嵌套了列表渲染，防止`item`与`key`指代不明，可以改名
```html
<view wx:for="{{students}}" wx:key="name" wx:for-index="i" wx:for-item="element">
  {{i}}: {{element.name}} 已经 {{element.age}} 岁了
</view>
```

## 四、条件渲染

**wx:if**：
- `wx:if={{show}}`
- `wx:elif={{show}}`
- `wx:else`

**hidden**：
- `hidden="{{!show}}` 布尔值为`ture`时，元素隐藏

**block**：
- 用于分组控制页面元素的渲染，通过配合 `wx:for` 和 `wx:if` 来使用
- 以下列表只有在长度大于 0 时才渲染，否则给用户一个提示信息
```html
<block wx:if="{{isLogin}}"> 
	<view class="msg">{{msg}}</view> 
	<input name="number" value="{{number}}" /> 
	<!-- 列表数据渲染 --> 
	<view class="students">...</view> 
	<!-- 条件数据渲染 --> 
	<button type="primary" bind:tap="toggle">显示/隐藏</button> 
	<view wx:if="{{seen}}">{{msg}}</view> 
	<view hidden="{{!seen}}">{{msg}}</view> 
	<view wx:if="{{isLogin}}">完善用户信息</view> 
	<view wx:else>请先登录</view> 
</block> 
<view wx:else class="tips">空空如也~</view>
```

## 五、列表渲染时事件函数传参

### 传值
- 方法一：`mark:参数名="{{属性名}}"` 小程序专用方法
```html
<view wx:for="{{heroList}}" wx:key="*this" mark:index="{{index}}" bind:tap="delHero">
```

- 方法二：`data-参数名="{{属性名}}"` 原生自定义属性方法
```html
<view wx:for="{{heroList}}" wx:key="*this" data-index="{{index}}" bind:tap="delHero">
```

### 接收
- mark→`event.mark.属性名`
- data-xxx→`event.target.dataset.属性名`
```js
  // 使用event/ev/e来接收参数
  // 接收到不是一个传值, 而是整个事件对象
  delFruit(event) {
    // 这里是两种传参方式的接收办法
    console.log('mark 语法', event.mark.index);
    console.log('自定义属性data-xxx', event.target.dataset.index);
    // 删除数据
    this.data.list.splice(event.mark.index, 1)
    // 更新视图
    this.setData({
      list: this.data.list
    })
  }
```

## PS: `this.setData`赋值/更新的特殊情况

**正常赋值的同时更新：**
```js
	add() {
		this.setData({
			num : this.data.num + 1
	})
```

**先进行数组操作再更新：**
原因：很多数组操作都有返回值，如果在操作同时更新则会把返回值赋给数据，分开写可以抛弃掉返回值而只操作数据本身，再更新视图层数据即可
```js
addHero() {
	this.data.heroList.push(this.data.hero)
	this.setData({
		heroList: this.data.heroList
	})
},
```

```js
delHero(event) {
	this.data.heroList.splice(event.mark.index,1)
	this.setData({
		heroList: this.data.heroList
	})
}
```


