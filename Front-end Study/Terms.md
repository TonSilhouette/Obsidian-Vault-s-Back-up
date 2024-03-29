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

**热重载 (Hot Reload)** 是一个可以允许开发者在应用程序运行时更改源代码，并立即看到效果的关键功能。

**热重载、热重启和完全重启之间有什么区别？**  

- **热重载** 会将代码更改转入 VM，重建 widget 树并保持应用的状态，整个过程不会重新运行 `main()` 或者 `initState()`。（在 IDEA 中的快捷键是 `⌘\`，在 VSCode 中是 `⌃F5`）
    
- **热重启** 会将代码更改转入 VM，重启 Flutter 应用，不保留应用状态。（在 IDEA 中的快捷键是 `⇧⌘\`，在 VSCode 中是 `⇧⌘F5`）
    
- **完全重启** 将会完全重新运行应用。该进程较为耗时，因为它会重新编译原生部分代码。在 Web 平台上，它同时会重启 Dart 开发编译器。完全重启并没有既定的快捷键，你需要手动停止后重新运行。

## 预编译

**预编译**又称**预处理**，是整个编译过程最先做的工作，即程序执行前的一些预处理工作。主要处理`#`开头的指令。如拷贝`#include`包含的文件代码、替换`#define`定义的宏、条件编译`#if`等。

**何时需要预编译**
- 总是使用不经常改动的大型代码体。
- 程序由多个模块组成，所有模块都使用一组标准的包含文件和相同的编译选项。在这种情况下，可以将所有包含文件预编译为一个预编译头。

## 细粒度

**粗粒度&细粒度**
- 粗粒度与细粒度是一个相对的概念。它们的区别主要是出于重用的目的。
- 为尽可能重用，所以将一个复杂的类（粗粒度）拆分成高度重用的职责清晰的类(细粒度)。一个项目模块分得越多，每个子模块越小，负责的工作越细，说明粒度越细。

**粗细粒度&重轻量级**
- 粗细粒度：容纳逻辑的多少
- 重轻量级：占用的资源多少

**应用**
- 粒度这个词语一般在权限管理方面用的比较多，举个例子来说，对于一个模块级别或以上的，就可以叫做粗粒度，对于详细到记录级别的，就可以叫做细粒度。

## IDE

**集成开发环境**（IDE，Integrated Development Environment ）是用于提供程序开发环境的应用程序，一般包括代码编辑器、编译器、调试器和图形用户界面等工具。集成了代码编写功能、分析功能、编译功能、调试功能等一体化的开发软件服务套。所有具备这一特性的软件或者软件套（组）都可以叫集成开发环境。

## 构造函数与工厂函数

#### 工厂函数：
概念：用来生产 **相同属性** 的对象的函数，通过它我们可以构造出许多 **属性相同**，但拥有 **不同属性值** 的对象

代码：
```js
    function factory(name, age) {
        const obj = {}
        obj.name = name
        obj.age = age
        return obj
    }
    var obj = factory('carl','28')
```

生产过程：
1. 接受自定义属性值
2. 新建一个对象
3. 给新建的对象赋值自定义属性
4. 返回新创建的对象

#### 构造函数：
概念：用来生产新对象的函数

代码：
```js
    function Constructor(name,age){
        this.name = name
        this.age = age
    }
    
    var obj = new Constructor('carl','28')
```

构造过程：
1. 接受自定义属性值
2. 将自定义属性赋值给 **this**

#### 区别：
1. 工厂函数的使用不借助 **new**，不使用 **this**，而构造函数需要
2. 工厂函数需要 **明确** 返回新对象，而构造函数不需要

原因：**new**帮忙做了很多事
1. **创建** 一个新对象
2. 将函数内部的this **指向** 这个新对象
3. 将 **新对象的原型对象** 指向 **构造函数的原型对象**
4. **返回** this