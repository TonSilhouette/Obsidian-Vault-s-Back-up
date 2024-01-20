## types文件夹统一声明类型

- 所有文件以`.d.ts`结尾
- 无需暴露，VSCode运行时会先寻找`.d.ts`文件???
### 1.组件类型文件.d.ts
```ts file:src/types/components.d.ts
import NavBar from '@/components/NavBar.vue'

// 使用typeof直接借用组件的类型
declare module 'vue' {
	interface GlobalComponents {
		NavBar: typeof NavBar
	}
}
```

```ts file:src/components/NavBar.vue

```

```ts file:vite.config.ts
// 按需加载所用的插件
import Components from 'unplugin-vue-components/vite'
import { VantResolver } from '@vant/auto-import-resolver'

export default defineConfig({
	plugins: [
		vue(),
		Components({
			// 不要自动生成类型声明文件我们后面会自己定义
			dts: false,
			resolvers: [VantResolver({ importStyle: false })]
		}),
	],
	...
})
```

### 2.常规类型文件.d.ts
```ts file:src/types/user.d.ts
export interface UserType {
	token?: string
	id?: string
	account?: string
	mobile?: string
	avatar?: string
	refreshToken?: string
}
```

- 导入类型文件时，如果仅仅用作类型声明，需要声明`type`
```ts
import type { UserType } from '@/types/user'
```
