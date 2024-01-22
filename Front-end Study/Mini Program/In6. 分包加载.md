## 一、基础概念

**主包&分包：**
- 在构建小程序分包项目时，构建会输出一个或多个分包。使用分包的小程序必定有一个**主包**
- 主包，放置着默认启动页面/TabBar 页面，以及每个分包都需用到公共资源/JS 脚本；
- 而**分包**则是根据开发者的配置进行划分

**加载流程：**
- 在小程序启动时，会默认下载主包并启动主包内页面
- 当用户进入分包内某个页面时，客户端会把对应分包下载下来，下载完成后再进行展示

**分包大小限制：**
- 整个小程序所有分包大小不超过 20M（开通虚拟支付后的小游戏不超过30M）
- 单个分包/主包大小不能超过 2M

## 二、使用分包

1. 移动文件
	- 将需要分包的文件放入根目录下的独立文件，命名`packageXX` / `xx_pkg`
	- 文件包括`pages`页面四件套，`static`静态资源
```
├── app.js
├── app.json
├── app.wxss
├── packageA
│   ├── pages
│   │    ├── cat
│   │    │    ├── index.js
│   │    │    ├── index.json
│   │    │    ├── index.scss / index.wxss
│   │    │    └── index.wxml
│   │    └── dog
│   └── static
│       ├── images
│       └── tabs
├── packageB
│   └── pages
│       ├── apple
│       └── banana
├── pages
│   ├── index
│   └── logs
└── utils
```

2. 声明分包
	- 通过在 app.json `subPackages` 字段声明项目分包结构
	- 静态资源不用声明路径，但是之前引用的需要改正引用路径
```js
{
  "pages":[
    "pages/index",
    "pages/logs"
  ],
  "subpackages": [
    {
      "root": "packageA",
      "pages": [
        "pages/cat",
        "pages/dog"
      ]
    }, {
      "root": "packageB",
      "name": "pack2",
      "pages": [
        "pages/apple",
        "pages/banana"
      ]
    }
  ]
}```

3. 打包原则
	- 声明 `subpackages` 后，将按 `subpackages` 配置路径进行打包，配置路径外的目录将被打包到主包中
	- 主包也可以有自己的 pages，即最外层的 pages 字段。
	- `subpackage` 的根目录不能是另外一个 `subpackage` 内的子目录
	- `tabBar` 页面必须在主包内
	
4. 引用原则
	- `packageA` 无法 require `packageB` JS 文件，但可以 require 主包、`packageA` 内的 JS 文件；使用 [分包异步化](https://developers.weixin.qq.com/miniprogram/dev/framework/subpackages/async.html) 时不受此条限制
	- `packageA` 无法 import `packageB` 的 template，但可以 require 主包、`packageA` 内的 template
	- `packageA` 无法使用 `packageB` 的资源，但可以使用主包、`packageA` 内的资源

## 三、分包预下载