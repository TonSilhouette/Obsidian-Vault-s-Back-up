## 一、实例和组件定义data的区别
创建`vue`实例的时候定义`data`属性既可以是一个对象，也可以是一个函数
```js
const app = new Vue({
    el:"#app",
    // 对象格式
    data:{
        foo:"foo"
    },
    // 函数格式
    data(){
        return {
             foo:"foo"
        }
    }
})
```

组件中定义`data`属性，只能是一个函数，如果定义为一个对象则会报错："返回的`data`应该是一个函数在每一个组件实例中"

## 二、组件data定义函数与对象的区别

`vue`初始化`data`的源码：
```js
function initData (vm: Component) {
  let data = vm.$options.data
  data = vm._data = typeof data === 'function'
    ? getData(data, vm)
    : data || {}
    // 三元表达式，允许data为object或function
    ...
}
```

自定义组件会进入`mergeOptions`进行选项合并：
```js
Vue.prototype._init = function (options?: Object) {
    ...
    // merge options
    if (options && options._isComponent) {
      // optimize internal component instantiation
      // since dynamic options merging is pretty slow, and none of the
      // internal component options needs special treatment.
      initInternalComponent(vm, options)
    } else {
      vm.$options = mergeOptions(
        resolveConstructorOptions(vm.constructor),
        options || {},
        vm
      )
    }
    ...
  }
```

定义`data`会进行数据校验，这时候`vm`实例为`undefined`，进入`if`判断，若`data`类型不是`function`，则出现警告提示：
```js
strats.data = function (
  parentVal: any,
  childVal: any,
  vm?: Component
): ?Function {
  if (!vm) {
    if (childVal && typeof childVal !== "function") {
      process.env.NODE_ENV !== "production" &&
        warn(
          'The "data" option should be a function ' +
            "that returns a per-instance value in component " +
            "definitions.",
          vm
        );

      return parentVal;
    }
    return mergeDataOrFn(parentVal, childVal);
  }
  return mergeDataOrFn(parentVal, childVal, vm);
};
```

#### 结论
- `vue`**根实例**的属性`data`既可以是*对象*也可以是*函数*（根实例是单例），不会产生数据污染

- **组件**的data定义为*对象*，会导致多个组件实例对象之间共用一个`data`，产生数据污染；
   定义为*函数*的形式，`initData`时会将其作为工厂函数，会返回全新`data`对象，不会产生数据污染