## 双链
`[[]]`以link的形式插入note。这个可以通过`[[filename#header]]`的方式引用到更细的层级，也可以用`[[filename|代替文本]]`的方式进行文本替换  
`![[]]`会将插入的note显示在当前页，类似图片。这个等价于`![](note名字)`  
`[链接文字](note name)`会以链接方式插入note。这个和`[[]]`不同在于，这种方式不会自动显示note名字，需要在链接文字中填写。

## Emoji 😜
`Control + Command + SPACE`

## Obsidian Git 报错

```bash
// error:
Another git process seems to be running in this repository, e.g.
an editor opened by 'git commit'. Please make sure all processes
are terminated then try again. If it still fails, a git process
may have crashed in this repository earlier:
remove the file manually to continue.
```

- 原因：`git`在同时执行两个命令时，通常会出现此类问题；可能一种来自命令提示符，另一种来自 IDE。

- 解决方式：删除目录或工作树之一中的`index.lock`的文件
```bash
rm -f .git/index.lock
```