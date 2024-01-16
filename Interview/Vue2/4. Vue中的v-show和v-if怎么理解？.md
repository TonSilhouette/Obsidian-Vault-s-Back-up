## 一、v-show与v-if的共同点

1. 都能控制元素在页面是否显示
2. 用法上也是相同的，都是在元素标签内用`v-xx=“布尔值/表达式”`，表达式为`true`时显示/占据页面位置，表达式为`false`不显示/占据页面位置
## 二、v-show与v-if的区别

1. 控制手段不同
	`v-show`的显示隐藏是在修改元素`css`的`display`属性，`dom`元素还在
	`v-if`的显示隐藏是将`dom`元素反复创建及销毁
	
2. 编译过程不同
	`v-if`切换有一个局部编译/卸载的过程，切换过程会销毁和重建内部的事件监听和子组件，并且会会触发组件的生命周期钩子。true的时候触发创建的钩子，false的时候触发删除的钩子（因为不存在更新，也就不关beforeUpdate和updated的事了）
	`v-show`只是简单的基于css切换，没有编译，，不会触发生命周期的钩子
	
3. 性能消耗不同
	`v-if`有更高的切换消耗
	`v-show`有更高的初始渲染消耗

## 三、v-show与v-if的实现原理（源码）

### v-show原理
不管初始条件是什么，元素总是会被渲染
有`transition`就执行`transition`，没有就直接设置`display`属性
```js
// https://github.com/vuejs/vue-next/blob/3cd30c5245da0733f9eb6f29d220f39c46518162/packages/runtime-dom/src/directives/vShow.ts
export const vShow: ObjectDirective<VShowElement> = {
  beforeMount(el, { value }, { transition }) {
    el._vod = el.style.display === 'none' ? '' : el.style.display
    if (transition && value) {
      transition.beforeEnter(el)
    } else {
      setDisplay(el, value)
    }
  },
  mounted(el, { value }, { transition }) {
    if (transition && value) {
      transition.enter(el)
    }
  },
  updated(el, { value, oldValue }, { transition }) {
    // ...
  },
  beforeUnmount(el, { value }) {
    setDisplay(el, value)
  }
}
```

### v-if原理
返回一个`node`节点，`render`函数通过表达式的值来决定是否生成`DOM`
```js
// https://github.com/vuejs/vue-next/blob/cdc9f336fd/packages/compiler-core/src/transforms/vIf.ts
export const transformIf = createStructuralDirectiveTransform(
  /^(if|else|else-if)$/,
  (node, dir, context) => {
    return processIf(node, dir, context, (ifNode, branch, isRoot) => {
      // ...
      return () => {
        if (isRoot) {
          ifNode.codegenNode = createCodegenNodeForBranch(
            branch,
            key,
            context
          ) as IfConditionalExpression
        } else {
          // attach this branch's codegen node to the v-if root.
          const parentCondition = getParentCondition(ifNode.codegenNode!)
          parentCondition.alternate = createCodegenNodeForBranch(
            branch,
            key + ifNode.branches.length - 1,
            context
          )
        }
      }
    })
  }
)
```
## 四、v-show与v-if的使用场景

- 如果需要非常频繁地切换，使用 v-show 较好
- 如果在运行时条件很少改变，使用 v-if 较好