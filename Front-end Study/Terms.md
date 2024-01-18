## 字面量
字面量 （literal，直译: 文本代码）就是指变量的常数值，字面上所看到的值。

分类：
1. **字符串字面量 (string literal)**
    指双引号引住的一系列字符。如 `var test="hello world!"`中的`"hello world!"`
2. **数组字面量 (array literal)**
	如`var team=["tom","john","smith"]`中的`["tom","john","smith"]`
3. **对象字面量 (object literal)**
	如`var person={name:"tom",age:"26"}`中的`{name:"tom",age:"26"}`
4. **函数字面量 (function literal)**
	```js
	var person={
    name:"tom",
    age:"23",
    tell:function(){alert(name);}
	}
	```
	其中`tell`的值`function{alert(name)}`是函数字面量

## 单文件组件 (SFC)

**非单文件组件**
- 在很多 Vue 项目中，我们使用 `Vue.component` 来定义全局组件，紧接着用 `new Vue({ el: '#container '})` 在每个页面内指定一个容器元素
- 这种方式在很多中小规模的项目中运作的很好，在这些项目里 JavaScript 只被用来加强特定的视图。但当在更复杂的项目中，或者你的前端完全由 JavaScript 驱动的时候，下面这些缺点将变得非常明显：
	- **全局定义 (Global definitions)** 强制要求每个 component 中的命名不得重复
	- **字符串模板 (String templates)** 缺乏语法高亮，在 HTML 有多行的时候，需要用到丑陋的 `\`
	- **不支持 CSS (No CSS support)** 意味着当 HTML 和 JavaScript 组件化时，CSS 明显被遗漏
	- **没有构建步骤 (No build step)** 限制只能使用 HTML 和 ES5 JavaScript，而不能使用预处理器，如 Pug (formerly Jade) 和 Babel

**单文件组件**
- 文件扩展名为 `.vue` 的 **single-file components (单文件组件)** 为以上所有问题提供了解决方法，并且还可以使用 webpack 或 Browserify 等构建工具
- 优点：
	- 完整语法高亮
	- CommonJS 模块
	- 组件作用域的 CSS

## 热重载


## 预编译


## 细粒度


## IDE

**集成开发环境**（IDE，Integrated Development Environment ）是用于提供程序开发环境的应用程序，一般包括代码编辑器、编译器、调试器和图形用户界面等工具。集成了代码编写功能、分析功能、编译功能、调试功能等一体化的开发软件服务套。所有具备这一特性的软件或者软件套（组）都可以叫集成开发环境。