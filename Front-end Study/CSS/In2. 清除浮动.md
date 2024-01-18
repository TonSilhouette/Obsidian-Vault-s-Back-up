## 一、清除浮动是在清除什么？

是在**清除浮动造成的影响**：解决因为子元素浮动脱标，造成父元素高度为0的问题

解决思路：父级自动检测并适应浮动的子盒子的高度

## 二、方法
### 1. 额外标签法💩
在子元素的最后添加一个额外标签div，类名为`clearfix`
在css中选择类名`.clearfix` ，添加属性`{clear:both;}`

缺点：代码冗余，用一次就要写一个div标签

### 2. overflow法💩
父元素添加`overflow: hidden`

缺点：可能隐藏本想溢出的内容

### 3. 单伪元素法👏🏼
```css
.clearfix::after {
	content: ""; /*必需属性*/
	display: block; /*行内伪元素→块级伪元素*/
	clear: both; 
}
```

优点：专属类名，谁用给谁
### 4. 双伪元素法👏🏼
```css
.clearfix::before,.clearfix::after {
	content: "";   
	display: table;  /*此元素会作为块级表格来显示，表格前后带有换行符*/
} 
.clearfix::after {   
	clear: both; 
}
```

优点：除了可以清除浮动影响以外，还可以处理margin的塌陷问题