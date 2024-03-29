
一句话：**利用全局自定义组件与条件渲染配合实现权限校验**

## 一、封装全局自定义组件`authorization`

封装方法：[[In7. 自定义组件与传参#一、基础使用|自定义组件的基础使用]]

- 在`authorization`组件的`attached`生命周期中去获取在本地或者`app`里存储的token，并根据有无token来改变变量`isLogin`的布尔值
- 在`authorization`组件的`wxml`页面里放入内容，添加`wx:if`绑定`isLogin`来判断有无登录
- 在需要权限校验的`wxml`页面，用`authorization`标签包住内容就可以控制登录了就显示，未登录就不显示
## 二、使用插槽提高拓展性

- 目前有一个问题，`authorization`标签内部显示什么是由`authorization`的wxml里的内容决定的，如果写死的话，这个组件显示的内容就是固定的
- 可以在`authorization`的wxml里放入一个匿名插槽，并把条件渲染与插槽绑定
- 这样`authorization`组件显示什么就由标签包住的内容决定了，同时也有权限控制的效果

```html 
<!--components/authorization/authorization.wxml-->
<slot wx:if="{{isLogin}}"></slot>
```

```html
<authorization>
	需要权限控制的内容
</authorization>
```