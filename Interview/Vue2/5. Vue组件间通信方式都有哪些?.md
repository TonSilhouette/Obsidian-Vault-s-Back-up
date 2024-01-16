## 一、组件间通信的概念
**组件**是`vue`最强大的功能之一，`vue`中每一个`.vue`我们都可以视之为一个组件。
**通信**是指通过某种方式来传递信息。
**组件间通信**即不同组件之间互相传值。
## 二、组件间通信的方式与适用场景
### 1. props 传递
- 适用场景：父传子

- 子组件设置`props`属性，定义接收父组件传递过来的参数
`Children.vue` （子要）
```js
props:{  
	// 字符串形式  
	要传递的数据名:{
		// 必须要传，不必须就不写
		required: true,        
		// 限制要传递的类型
		type: 类型,        
		// 设置默认值，对象类型必须有默认值
		default: 默认值,
	}
}
	```

==props类型及默认值==
```js
props: {
	demoString: {
	  type: String,
	  default: ''
	},
	demoNumber: {
	  type: Number,
	  default: 0
	},
	demoBoolean: {
	  type: Boolean,
	  default: true
	},
	demoArray: {
	  type: Array,
	  default: () => []
	},
	// 默认值为对象时,必须要加()，否则返回的是一个空函数体，没有返回值
	demoObject: {
	  type: Object,
	  default: () => ({})
	// 完整写法：
	//	default: function () {
	//	return {}
	//	}
	},
	demoFunction: {
	  type: Function,
	  default: function () {}
	}
}
```

- 父组件在子组件标签中通过字面量来传递值
`Father.vue`（父给）
```js
<Children :数据名="要给的数据" />
```
### 2. $emit 触发自定义事件
- 适用场景：子传父

- 子组件通过`$emit`触发自定义事件，`$emit`第二个参数为传递的数值
`Children.vue` （子通知）
```js
$emit('自定义的事件名', 传递的数据)
```

- 父组件绑定监听器获取到子组件传递过来的参数
`Father.vue` （父接收）
```js
<Children @事件名="函数" />
```
### 3. ref 
- 适用场景：父传子

- 父组件在使用子组件的时候设置`ref`
- 父组件通过设置子组件`ref`来获取数据
`Father.vue` 
```js
<Children ref="son" />  
  
this.$refs.son  // 获取子组件实例，$ref.子组件 = 调用子组件里的this
```

### 4. EventBus
- 使用场景：兄弟组件传值
- 创建一个中央时间总线`EventBus`

`Bus.js`
```js
// 创建一个中央时间总线类  
class Bus {  
  constructor() {  
    this.callbacks = {};   // 存放事件的名字  
  }  
  $on(name, fn) {  
    this.callbacks[name] = this.callbacks[name] || [];  
    this.callbacks[name].push(fn);  
  }  
  $emit(name, args) {  
    if (this.callbacks[name]) {  
      this.callbacks[name].forEach((cb) => cb(args));  
    }  
  }  
}  
  
// main.js  
Vue.prototype.$bus = new Bus() // 将$bus挂载到vue实例的原型上  
// 另一种方式  
Vue.prototype.$bus = new Vue() // Vue已经实现了Bus的功能
```

- 兄弟组件通过`$emit`触发自定义事件，`$emit`第二个参数为传递的数值
`Sibling1.vue`
```js
this.$bus.$emit('foo')
```

- 另一个兄弟组件通过`$on`监听自定义事件
`Sibling2.vue`
```js
this.$bus.$on('foo', this.handle)
```

### 5. $parent
- 使用场景：兄弟组件传值
- 通过共同父辈`$parent` (或者`$root`) 搭建通信桥梁
- 祖辈则需`$parent.$parent...`一级一级往上找
- 缺点：父级是相对的，HTML结构的改变会影响父级层级

`Sibling1.vue`
```js
this.$parent.on('事件名',this.add)
```

- 另一个兄弟组件通过`$on`监听自定义事件
`Sibling2.vue`
```js
this.$parent.emit('事件名')
```

### 6. $attrs
- 适用场景：祖先传递数据给子孙
- 设置批量向下传属性`$attrs`和 `$listeners`
- 包含了父级作用域中不作为 `prop` 被识别 (且获取) 的特性绑定 ( class 和 style 除外)。
- 可以通过 `v-bind="$attrs"` 传⼊内部组件

```js
// child：并未在props中声明foo  
<p>{{$attrs.foo}}</p>  
  
// parent  
<HelloWorld foo="foo"/>
```

```js
// 给Grandson隔代传值，communication/index.vue  
<Child2 msg="lalala" @some-event="onSomeEvent"></Child2>  
  
// Child2做展开  
<Grandson v-bind="$attrs" v-on="$listeners"></Grandson>  
  
// Grandson使⽤  
<div @click="$emit('some-event', 'msg from grandson')">  
{{msg}}  
</div>
```

### 7. provide 与 inject
- 在祖先组件定义`provide`属性，返回传递的值
`Ancestor.vue`
```js
provide(){  
    return {  
        foo:'foo'  
    }  
}
```

- 在后代组件通过`inject`接收组件传递过来的值
`Offspring.vue`
```js
inject:['foo']
```

### 8. Vuex
- 适用场景: 复杂关系的组件数据传递

### 小结
- 父子关系的组件数据传递选择 `props`  与 `$emit`进行传递，也可选择`ref`
- 兄弟关系的组件数据传递可选择`EventBus`，其次可以选择`$parent`进行传递
- 祖先与后代组件数据传递可选择`attrs`与`listeners`或者 `provide`与 `inject`
- 复杂关系的组件数据传递可以通过`vuex`存放共享的变量