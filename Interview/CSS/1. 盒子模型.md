## 一、盒模型是什么

当对一个文档进行布局的时候，浏览器的渲染引擎会根据标准之一的 CSS 基础框盒模型（CSS basic box model），将所有元素表示为一个个矩形的盒子。、】

**组成：**
1. content，实际内容
2. border，边框
3. padding，内边距（取值不能为负）
4. margin，外边距（在元素外创建额外的空白，空白指不能放其他元素的区域）

## 二、盒模型分类

### 1. (W3C) 标准盒模型
是浏览器默认的盒子模型，默认属性`box-sizing: content-box`

- 盒子总宽度 = width + padding + border + margin
- 盒子总高度 = height + padding + border + margin
即：width、height代表的是content的宽高，不包含 padding 和 border

所以：给盒子设置的`width/height`值不一定等于盒子实际的宽与高
### 2. (IE) 怪异盒模型
通过`box-sizing: border-box`属性设置为怪异盒模型

- 盒子总宽度 = height + margin
- 盒子总高度  = width + margin
即：width、height代表的是content + padding + border的宽高

**应用场景**：
两个盒子宽高给了50%后又设置padding或margin，会导致换行
以及其他不想被padding和margin影响盒子宽高的情况

注：margin 虽然是盒子模型的组成部分，但是和盒子的大小没有关系，margin 决定的是盒子占据的位置

