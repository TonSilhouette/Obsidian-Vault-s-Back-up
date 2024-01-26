## 一、配置文件

``` bash
.env                # 在所有的环境中被载入
.env.local          # 在所有的环境中被载入，但会被 git 忽略
.env.[mode]         # 只在指定的模式中被载入
.env.[mode].local   # 只在指定的模式中被载入，但会被 git 忽略
```

## 二、声明与访问

### 声明环境变量的语法：
- 一个环境文件只包含环境变量的**键值对**
```
VUE_APP_大写的变量名 = 变量值
```

### 访问环境变量的语法：
```
process.env.VUE_APP_大写的变量名
```

它会自动根据当前环境的不同取不同的值，如果当前是开发环境，取的是`.env.development`里的值，如果当前是生产环境，取的是`.env.production`里的值

#### 模式
默认情况下，一个 Vue CLI 项目有三个模式：
- `development` 模式用于 `vue-cli-service serve`
- `test` 模式用于 `vue-cli-service test:unit`
- `production` 模式用于 `vue-cli-service build` 和 `vue-cli-service test:e2e`

当运行 `vue-cli-service` 命令时，所有的环境变量都从对应的**环境文件**中载入。如果文件内部不包含 `NODE_ENV` 变量，它的值将取决于模式，例如，在 `production` 模式下被设置为 `"production"`，在 `test` 模式下被设置为 `"test"`，默认则是 `"development"`。

#### 打包
- 环境变量会随着构建打包嵌入到输出代码
- 只有 `NODE_ENV`，`BASE_URL` 和以 `VUE_APP_` 开头的变量将通过 `webpack.DefinePlugin` 静态地嵌入到客户端侧的代码中。这是为了避免意外公开机器上可能具有相同名称的私钥
- 被载入的变量将会对 `vue-cli-service` 的所有命令、插件和依赖可用