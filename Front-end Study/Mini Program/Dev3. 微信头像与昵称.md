## 一、头像

- 使用与获取使用到了微信小程序的`button`组件
### 获取

```html
<button 
open-type="chooseAvatar" 
bind:chooseavatar="getUserAvatar" 
class="button" size="mini" 
hover-class="none"
>
	<image 
	class="avatar" 
	src="{{userInfo.avatar || '/static/images/avatar_1.jpg'}}"
	>
	</image>
</button>
```

- 设置属性`open-type`为`chooseAvatar`，即可点击头像触发微信自带的选择头像弹窗
- 绑定`chooseavatar`来获取到设置的头像的临时路径

### 修改

```js
getUserAvatar(ev) {
	// 得到用户选择的图片的临时地址
	console.log('ev.detail.avatarUrl', ev.detail.avatarUrl)
	this.updateUserAvatar(ev.detail.avatarUrl)
},
updateUserAvatar(avatarUrl) {
	// 调用接口上传图片
	wx.uploadFile({
		url: wx.http.baseURL + '/upload',
		filePath: avatarUrl,
		name: 'file',
		header: {
			Authorization: 'Bearer ' + wx.getStorageSync('token')
		},
		formData: {
			type: 'avatar',
		},
		success: (res) => {
			// 转换 json 数据
			const data = JSON.parse(res.data)
			// 检测接口调用结果
			if (data.code !== 10000) return wx.utils.toast('更新头像失败!')
			// 保存并预览图片地址
			this.setData({ 'userInfo.avatar': data.data.url })
		},
	})
},
```

- `chooseavatar`回调函数的`event`对象的`detail.avatarUrl`，就是头像图片的临时地址
- 要调用微信小程序上传文件的内置API`wx.uploadFile`，把图片上传到云服务器，属性有：
	- url：因为接口不是自己封装的request，需要手动拼接基地址
	- header：因为接口不是自己封装的request，需要手动注入token
	- filePath：临时头像地址
	- name：和后端商量
	- formData：`type: 'avatar'`，这样微信就知道这张图片要上传为头像，其他文件上传不用写这个属性
	- success回调函数：将返回的头像地址赋值给变量，使用setData渲染到头像
## 二、昵称

- 使用与获取使用到了微信小程序的`input`组件
- 也可以使用Vant组件库的`van-field`
### 获取

```html
<van-field 
bind:blur="getUserNickName" 
type="nickname" 
center 
label="昵称" 
input-align="right" 
placeholder="请输入昵称" 
value="{{userInfo.nickName || '微信用户'}}" 
/>
```

- 设置属性`type`为`nickname`，即可点击昵称输入框触发微信自带的选择昵称弹窗
- 绑定`blur`事件来获取选择的昵称

### 修改

```js
getUserNickName(ev) {
	console.log('ev.detail.value', ev.detail.value)
	// 更新用户昵称
	this.updateNickName(ev.detail.value)
},
async updateNickName(nickName) {
	// 请求数据接口
	const { code } = await wx.http.put('/userInfo', { nickName })
	// 检测接口调用的结果
	if (code !== 10000) return wx.utils.toast('更新用户信息失败!')
	// 保存用户昵称
	this.setData({ 'userInfo.nickName': nickName })
},
```

- `blur`事件回调函数的`event`对象的`detail.value`，就是昵称的字符串
- 调用自己封装的接口发请求更改昵称
- 直接将获取到的昵称赋值给变量，使用setData渲染到昵称

---

![[小程序头像和昵称编辑.jpg]]