## 一、概念
vue实例（组件）从创建到销毁所经历的全过程
## 二、生命周期阶段与钩子
Vue生命周期共有4个阶段，8个钩子：创建前后，挂载前后，更新前后，销毁前后。
以及一些特殊场景的生命周期钩子。

| 生命周期-钩子   | 调用时机                   |
|:--------------- |:-------------------------- |
| 1.beforeCreate  | 组件实例被创建前           |
| 2.created       | 组件实例创建后             |
| 3.beforeMount   | 组件挂载之前               |
| 4.mounted       | 组件挂载到实例上去之后     |
| 5.beforeUpdate  | 组件数据发生变化，更新之前 |
| 6.updated       | 数据数据更新之后           |
| 7.beforeDestroy | 组件实例销毁之前           |
| 8.destroyed     | 组件实例销毁之后           |
| + activated     | keep-alive缓存的组件激活时 |
| + deactivated   | keep-alive缓存的组件停用时 |
| + errorCaptured | 捕获到子孙组件的错误时     |

图示：
[![piDRyh6.jpg](https://s11.ax1x.com/2023/11/29/piDRyh6.jpg)](https://imgse.com/i/piDRyh6)

**beforeCreate -> created**
- 初始化`vue`实例，进行数据观测

**created**
- 完成数据观测，属性与方法的运算，`watch`、`event`事件回调的配置
- 可调用`methods`中的方法，访问和修改data数据触发响应式渲染`dom`，可通过`computed`和`watch`完成数据计算
- 此时`vm.$el` 并没有被创建

**created -> beforeMount**
- 判断是否存在`el`选项，若不存在则停止编译，直到调用`vm.$mount(el)`才会继续编译
- 优先级：`render` > `template` > `outerHTML`
- `vm.el`获取到的是挂载`DOM`的

**beforeMount**
- 在此阶段可获取到`vm.el`
- 此阶段`vm.el`虽已完成DOM初始化，但并未挂载在`el`选项上

**beforeMount -> mounted**
- 此阶段`vm.el`完成挂载，`vm.$el`生成的`DOM`替换了`el`选项所对应的`DOM`

**mounted**
- `vm.el`已完成`DOM`的挂载与渲染，此刻打印`vm.$el`，发现之前的挂载点及内容已被替换成新的DOM

**beforeUpdate**
- 更新的数据必须是被渲染在模板上的（`el`、`template`、`render`之一）
- 此时`view`层还未更新
- 若在`beforeUpdate`中再次修改数据，不会再次触发更新方法

**updated**
- 完成`view`层的更新
- 若在`updated`中再次修改数据，会再次触发更新方法（`beforeUpdate`、`updated`）

**beforeDestroy**
- 实例被销毁前调用，此时实例属性与方法仍可访问

**destroyed**
- 完全销毁一个实例。可清理它与其它实例的连接，解绑它的全部指令及事件监听器
- 并不能清除DOM，仅仅销毁实例

**使用场景分析**

|生命周期|描述|
|:--|:--|
|beforeCreate|执行时组件实例还未创建，通常用于插件开发中执行一些初始化任务|
|created|组件初始化完毕，各种数据可以使用，常用于异步数据获取|
|beforeMount|未执行渲染、更新，dom未创建|
|mounted|初始化结束，dom已创建，可用于获取访问数据和dom元素|
|beforeUpdate|更新前，可用于获取更新前各种状态|
|updated|更新后，所有状态已是最新|
|beforeDestroy|销毁前，可用于一些定时器或订阅的取消|
|destroyed|组件已销毁，作用同上|