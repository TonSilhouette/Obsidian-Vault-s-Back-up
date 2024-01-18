## 一、什么是首屏加载

首屏时间（First Contentful Paint），指的是浏览器从响应用户输入网址地址，到首屏内容渲染完成的时间，此时整个网页不一定要全部渲染完成，但需要展示当前视窗需要的内容。

首屏加载是用户体验中**最重要**的环节。
### 计算首屏时间
```js
// 方案一：DOMContentLoad
document.addEventListener('DOMContentLoaded', (event) => {
    console.log('first contentful painting');
});
// 方案二：performance
performance.getEntriesByName("first-contentful-paint")[0].startTime

// performance.getEntriesByName("first-contentful-paint")[0]
// 会返回一个 PerformancePaintTiming的实例，结构如下：
{
  name: "first-contentful-paint",
  entryType: "paint",
  startTime: 507.80000002123415,
  duration: 0,
};
```

## 二、加载慢的原因

- 网络延时问题
- 资源文件体积过大
- 资源重复发送请求去加载了
- 加载脚本的时候，渲染内容堵塞了

## 三、解决方案
**<font color=teal>主要思路: </font>** **资源加载优化** 和 **页面渲染优化**

### 1. 减小入口文件体积--路由懒加载
默认情况下，所有的路由代码最终会被打包到一个js文件里，所有路由样式打包到一个css文件里，打开网站的时候就相当于把所有文件都下载下来了。

以函数的形式加载路由，这样就可以把不同路由对应的组件分割成代码块，分别打包，只有在解析给定的路由时，才会加载路由组件。使得入口文件变小，加载速度大大增加。

在`router`文件夹的`index.js`文件做导入时，将`import 组件 from '路径'`变成如下方式:
```js
	const 组件 = () => import('路径')
```

### 2. 静态资源本地缓存
合理利用`localStorage`

### 3. UI框架按需加载
UI组件库不要全部导入，要按需导入

### 4. 避免组件重复打包--minChunks
如果一个文件/库被多个路由使用，加载时就会重复下载

在`webpack`的`config`文件中，修改`CommonsChunkPlugin`的配置
```js
minChunks: 3
```

这段代码会把使用3次及以上的包抽离出来，放进公共依赖文件，避免了重复加载组件

### 5. 图片资源的压缩
对页面上使用到的`icon`，可以使用在线字体图标，或者雪碧图 (Sprite)，将众多小图标合并到同一张图上，以减轻`http`请求压力。

[![pidCmCD.png](https://s11.ax1x.com/2023/11/22/pidCmCD.png)](https://imgse.com/i/pidCmCD)