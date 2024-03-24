## 一、简单类型 (Primitives)

- 语法：`变量:类型`
- 作用：**为变量提供类型约束**，约定了什么类型，就只能赋值什么类型的值，否则报错
```ts
// 1.1 字符串
const str:string = 'hello'
// 1.2 数值
const num:number = 1
// 1.3 布尔值
const bool:boolean = true
// 1.4 undefined
const und:undefined = undefined
// 1.5 null
const nu:null = null
```

## 二、数组类型

数组关注的是**每一项/每个元素的类型**
### 1.基本使用
```ts
// 👏🏼写法一：
const strArr:string[] = ['a','b','c'];

// 🙅🏻‍♂️写法二：
const strArr: Array<string> = ['a','b','c'];
```

### 2.数组的联合类型
- 联合的类型需要用括号包裹，优先计算
```ts
const strNumArr: (string | number)[] = [1,'哈哈',2]
```

### 3.数组的类型别名
```ts
type SNArr = (string | number)[]
const strNumArr2: SNArr = [1,2,3,4,5, '张三']
```

## 三、函数类型

函数关注的是**参数的数量与类型**、**有无返回返回值与返回值的类型**

### 1.基本使用
```ts
// 函数声明：
function addFn(num1:number , num2:number):number {
	return num1 + num2
}
addFn(2,4)

// 箭头函数声明：
const addFn = (num1:number , num2:number):number => {
	return num1 + num2
}
addFn(2,4)
```

### 2.函数的类型别名
```ts
type MinFn = (num1:number , num2:number) => number
const minFn:MinFn = (num1,num2) => num1 - num2
minFn(10,4)
```

### 3.函数的特殊返回值类型 —— void
- void : 空返回值，即不返回任何值
- 使用类型别名时：不强制不返回值，任何返回值都会被忽略
- 使用直接声明时：强制不能返回任何值

#### void & undefined 区别：
1. undefined使用类型别名或直接声明，都强制返回undefined，不return时默认返回undefined
2. 函数未定义返回值类型，且不return时，默认返回void

### 4.函数的可选参数
- 冒号前加问号，即为可选（本质：定义类型 | undefined）
```ts
type MySliceType = (start?: number, end?: number) => void
```

## 四、对象类型

对象关注的是：
- ***属性* 的数量与类型**；
- ***方法* 的参数的数量与类型、有无返回返回值与返回值的类型**

### 1.基本使用
```ts
const student: {
	// 类型注解
	name: string,
	age: number,
	doHomework(): void
} = {
	// 真正赋值
	name: '张三',
	age: 16,
	doHomework() {}
}
```

### 2.对象的类型别名
```ts
type StudentType = {
	// 指定应该有的属性
	name: string,
	age: number,
	// 指定应该有的方法
	doHomework(): void
}

const student2: StudentType = {
	name: '李四',
	age: 18,
	doHomework() {}
}
```

### 3.对象的可选属性
```ts hl:4
type NewStudentType = {
	name: string
	age: number
	year?: number
	doHomework(): void
}
```

### 4.声明对象类型的特殊方式 —— interface
- interface 接口
- 作用：声明对象类型，是*直接声明*与*类型别名*之外的一种声明对象类型的方式
#### 4.1 基本使用
```ts
interface Person {
	name: string
	age: number
	sayHi: () => void
}
let person: Person = {
	name: 'jack',
	age: 19,
	sayHi() {},
};
```

#### 4.2 接口继承
- 类型拓展的方式之一
- 使用`extends`
```ts
interface Point2D {
	x: number;
	y: number;
}
// 继承 Point2D
interface Point3D extends Point2D {
	z: number;
}
// 继承后 Point3D 的结构：{ x: number; y: number; z: number }
```

#### 4.3 交叉类型
- 类型拓展的方式之一
- 使用`&`
```ts
type Point2DType = {
  x: number,
  y: number
}
// 交叉 Point2D
type Point3DType = Point2DType & {
  z: number
}
// 交叉后 Point3DType 的结构：{ x: number; y: number; z: number }
```

#### 4.4 Interface VS Type Aliase
**相同点：** 
- 都是声明类型的方式，便于类型的复用
- 都可以实现TS类型的拓展

**不同点：**

|          | **Interface** | **Type Aliase** |
| :------- | :------------ | --------------- |
| **支持类型** | 对象类型          | 包含对象类型的所有类型     |
| **拓展方式** | 继承`extends`   | 交叉`&`           |
| **重复定义** | 合并            | 不允许，会报错         |

## 五、字面量类型 (Literal Types)

- 字面量的值本身被作为类型，是一种更精确的类型声明
- 任意的 JS 值都可以作为字面量类型使用
- 一般都是搭配联合类型 `|` 写法来限制数据数量

```ts
type GenderType = 1 | 0
const myGender: GenderType = 0
```

## 六、枚举 (Enums)

- 枚举的功能类似于**字面量类型+联合类型组合**的功能，也可以表示一组明确的可选值
- 语法：
	1. 使用 `enum` 关键字定义枚举
	2. 约定枚举名称以大写字母开头
	3. 枚举中的多个值之间通过逗号`,`分隔
	4. 定义好枚举后，直接使用枚举名称作为类型注解

### 1.数字枚举
- 枚举成员的值默认从0开始，并按顺序自增
- 可以给枚举中的成员初始化值，后面的值会延续初始化值自增
- 自增的目的：不关心成员的值本身，只关心成员的属性，值只是为了避免重复
```ts
enum Direction {Up, Down, Left = 33, Right}

// 定义一个改方向的函数, 可以传入一个方向枚举数据, 我们会打印出来
function changeDirection(data: Direction) {
  console.log(data);
}

changeDirection(Direction.Up) // 0
changeDirection(Direction.Down) // 1
changeDirection(Direction.Left) // 33
changeDirection(Direction.Right) // 34
```

### 2.字符串枚举
```ts
enum Gender {
  male = '男',
  female = '女'
}
```

## 七、泛型 (Generics)

- 软件工程中，我们不仅要创建一致的定义良好的API，同时也要考虑**可复用性**。 组件不仅能够支持当前的数据类型，同时也能支持未来的数据类型，这在创建大型系统时为你提供了十分灵活的功能。
- 在TypeScript中，泛型是一种创建**可复用**代码组件的工具。这种组件不只能被一种类型使用，而是能被多种类型复用。类似于参数的作用，泛型是一种用以增强**类型别名、接口、函数类型**等能力的非常可靠的手段。

### 1.泛型函数
```ts
// 不固定类型, 只要求参数和返回值类型相同的泛型写法
function logId<T>(id: T): T {
  return id
}
const res1 = logId<number>(666)
const res2 = logId<string>('bye')
```

### 2.泛型类型别名
语法：`type 类型别名<类型参数>`
```ts
// 用类型别名声明泛型
type ResType<T> = {
  code: number
  message: string
  data: T
}
// 复用泛型类型别名
type HouseListType = ResType<{
  id: number
  point: string
  building: string
  room: string
}[]
>
// HouseListType类型注解完成版：
type HouseListType = {
  code: number
  message: string
  data: {
    id: number
  point: string
  building: string
  room: string
  }
}[]
```

### 3.泛型接口
语法：`interface 类型别名<类型参数>`
```ts
// 用接口声明泛型
interface ResType<T> {
  code: number
  message: string
  data: T
}
// 用接口声明数据
interface UserData {
  id: string
  avatar: string
  nickName: string
}
// 复用泛型接口
const resProfile: ResType<UserData> = {
  code: 0,
  message: '成功',
  data: {
    id: '123',
    avatar: 'avatar.png',
    nickName: '张三'
  }
}
```


## 八、实用类型 (Utility Types)

TS 内置的 [实用类型](https://link.juejin.cn/?target=https%3A%2F%2Fwww.typescriptlang.org%2Fdocs%2Fhandbook%2Futility-types.html "https://www.typescriptlang.org/docs/handbook/utility-types.html")，用于类型转换

### 1.`Partial<Type>`
接收一个泛型参数 `T`，将 `T` 的所有属性设置为**可选**
```ts
type Student = {
  name: string,
  age: number
}
// 全部属性变为可选
const myStudent: Partial<Student> = {}
```

### 2.`Readonly<Type>`
接收一个泛型参数 `T`，将 `T` 的所有属性设置为**只读**
```ts
// 全部属性变为只读
const myStudent: Readonly<Student> = {}
```

### 3.`Pick<Type,Keys>`
从一个对象类型中**选取**某些属性，并创建一个新的对象类型
接收两个泛型参数
- T：是对象类型名称
- K：是选择的属性名称，可以使用联合类型
```ts
interface Props {
  id: string
  title: string
  children: number[]
}
type PickProps = Pick<Props, 'id' | 'title'>
```

### 4.`Omit<Type,Keys>`
从一个对象类型中**剔除**某些属性，并创建一个新的对象类型
接收两个泛型参数
- T：是对象类型名称
- K：是剔除的属性名称，可以使用联合类型
```ts
type Props {
	name: string
	age: number
	hobby: string
	sex: “男" | "女"
}

// 如果我不希望有hobby和sex这两个属性，可以这么写
type NewProps = Omit<Props, "hobby" | "sex">
// 等价于
type NewProps  {
  name: string
  age: number
}
```

## 九、Any 类型

作用：逃避 TS 的类型检查，添加该类型后变量会被TS语法检查忽视

1. 显式声明
```ts
let obj: any = { age: 18 }
obj.bar = 100
obj()
const n: number = obj
// 均不会报错
```

2. 隐式声明
```ts
// 声明变量不给类型或初始值
let a; 
// 函数参数不给类型或初始值 
const fn = (n) => {}
```

## PS：类型推断 / 字面量推导 (Literal Inference)

在 TS 中存在类型推断机制，在没有指定类型的情况下，TS 也会给变量**推断**一个类型

两种推断场景：

1. 声明变量并初始化时
```ts
// 变量 age 的类型被自动推断为：number
let age = 18;
```

2. 决定函数返回值时
```ts
// 函数返回值的类型被自动推断为：number
const addFn = (num1: number, num2: number) => {
  return num1 + num2;
};
```

## PS：类型断言 (Type Assertions)

有时，我们会比TS更清楚一个值的类型，为了避免TS的报错，或者想更准确地限制类型，可以添加后缀：类型断言`as 具体类型`

场景一：获取dom元素
```ts
const dom = document.getElementById('#app')
// 此时dom变量的类型推断为 HTMLElement | null，因为可能获取不到

const dom = document.getElementById('#app') as HTMLElement
// 即可告知TS变量dom类型确定为HTMLElement
```

场景二：声明字面量类型时，传入的参数被错误识别为字符串，可以使用类型断言确定字面量
- 作用一：`as 具体字面量值` → 将值转化为字面量类型
```ts hl:8
function handleRequest(url: string, method: "GET" | "POST"): void {}
const req = { url: "https://example.com", method: "GET"};
handleRequest(req.url, req.method);
// 此时会报错：类型“string”的参数不能赋给类型“"GET" | "POST"”的参数
// 原因：method的值"GET"被识别为字符串类型而非字面量类型
// 解决：类型断言 -- as 具体字面量值
function handleRequest(url: string, method: "GET" | "POST"): void {}
const req = { url: "https://example.com", method: "GET" as "GET"};
handleRequest(req.url, req.method);
```

- 作用二：`as const` → 将复杂数据类型整体转化为字面量类型
```ts
const req = { url: "https://example.com", method: "GET" } as const;
handleRequest(req.url, req.method);
```

## PS：`Typeof` 类型运算符 (`Typeof` Type Operator)

TS又提供的一个获取数据类型的方法，可以在类型上下文中使用它来引用变量或属性的类型，并且直接当做类型注解来使用
```ts
const myPoint = {
  x: 666, // 类型推断:number
  y: 888  // 类型推断:number
}
function print2dPoint(point: typeof myPoint) {
// function print2dPoint(point: {x: number, y: number}) {
  console.log(point.x, point.y);
}
print2dPoint(myPoint)
```