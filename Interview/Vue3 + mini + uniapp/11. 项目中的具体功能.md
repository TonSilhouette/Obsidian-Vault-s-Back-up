## 一、电商分类模块

- 发请求获取分类模块的所有数据，数据结构为一个数组，数组的每一项又是一个数组，对应着一级目录与每个一级目录的二级目录，其中，每个二级目录中又有商品的数组
- 一级标题的渲染就直接遍历数据的第一层即可
- 二级标题的渲染与切换我使用了**计算属性**，通过一级标题的选中动态计算出二级数组
	- 其中，获取一级标题的选中状态，是通过声明一个变量`activeId`，点击了哪个一级标题就把当前的`id`赋值给`activeId`
	- 再根据`activeId`在计算属性中 find `id`并 return 出二级数组，同时还使用动态类来切换一级标题的高亮
- 二级标题使用`v-for`渲染后，再二次`v-for`二级标题下的商品，在列表渲染中要注意`item`不能重名，以及每一层循环中的`item`在数组中代表什么数据

```js
// 计算属性
const subCategories = computed(() => {
	// 没有数据就返回空数组,防止等待请求时报错
	if (categories.value.length === 0) return []
	// 查找当前的激活一级分类
	const current = categories.value.find(item => item.id === activeId.value)
	// 返回这个激活分类的子分类数组
	return current.children
})
```

```html
<!-- 分类左侧 -->
<view
@click="activeId = item.id"
:class="{ active: item.id === activeId }"
v-for="item in categories"
:key="item.id"
>		
{{ item.name }}
</view>
```
## 二、小程序的登录功能

无论哪种登录方式，整体逻辑：
1. 用户先授权允许小程序客户端获取到当前用户的信息，eg: 邮箱、手机号等等
2. 客户端将用户信息发送到微信服务器加密后返回
3. 客户端再通过调用微信的登录接口`wx.login`获取临时通行码`code`
4. 此时会把用户的加密信息和临时通行码发请求提供给后端，后端会把这些数据以及小程序所属的appid一起发送给微信进行验证，如果小程序验证该appid有权调用登录接口，就返回该用户的会话密钥（session_key）、微信开放平台账号下的唯一标识（unionid）和用户在当前小程序的唯一标识（openid）等`token`，后端保存用户的数据并返回客户端`token`，供登录后的一系列鉴权使用

代码实现步骤： 
1. 登录方式：手机号快速验证，使用button组件：
	- 固定属性即值  `open-type="getPhoneNumber"`，即可调出微信获取电话的询问
	- 固定事件监听 `@getphonenumber`，事件触发会返回事件参数，里面有用户的电话等加密信息
```html
<button open-type="getPhoneNumber" @getphonenumber="getPhoneByWeixin">
```

2. 接收参数，获取用户的加密信息，其中包括加密信息本体`encryptedData`，以及解密算法向量`iv`，两个配合才能解译密文。目的：在微信验证用户与小程序的权限前，公司无法获取到用户的信息
3. 调用内置API`uni.login()`，获取登录的临时通行码`code`
4. 将以上三个数据都放在参数里，发给后端（公司服务器）的登录接口
```js
const getPhoneByWeixin = async (ev) => {
	// 获取用户的加密信息
    const {encryptedData, iv} = ev.detail
    // 获取code
    const {code} = await uni.login()
    const res = await loginReal({
        encryptedData,
        iv,
        code
    })
}
```

5. 如果微信权限验证通过，会告知公司后端用户的相关信息和`token`，后端就可以储存用户信息，并给客户端提供`token`

## 三、小程序的登录流程

概述：
- 电商小程序，所以商品等浏览页面不需要登录，但添加购物车，收藏，支付等操作，以及进入我的页面都需要登录

进入的路径：
- 没有设置在tabBar或者声明式导航，而是使用了编程式导航跳转，在需要权限的页面进行校验是否登录，没有的话就直接跳转至登录页

数据的储存位置：
- 使用Pinia状态管理库，专门创建了一个`user.js`文件来存放用户信息仓库
- 在仓库内，定义了一个变量来存储用户的信息`profile`，声明了一个获取用户数据的登录函数`login()`，并且在该函数内将用户信息储存在`profile`里
- 在需要权限的页面，引入该仓库，获取`profile`变量，如果变量为空，则条件渲染页面或者直接跳转至登录页面
- 在登录页面，引入该仓库，获取`login()`函数，调用进行登录


