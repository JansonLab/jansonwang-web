---
title: "quartz4静态文件路径设置"
author: ["ubuntu"]
date: 2025-11-06T22:06:00+08:00
draft: false
---

遇到的问题：

1.  ORG MODE导出的静态文件保存到 ~/quartz/static/ 下
2.  quartz4 的默认静态文件是在 ~/quartz/quartz/static/
3.  需要更改 quartz4 的默认静态文件位置

怎么改：

1.  需要更改代码，代码路径 ~/quartz/quartz/plugins/emitters/static.ts
2.  找到：const staticPath = joinSegments(QUARTZ, "static")
3.  改为：const staticPath = joinSegments(QUARTZ, "../static")

遇到了新问题：
ORG MODE 生成的markdown文件中图片的路径少了 static 前缀


## quartz4 如何处理 markdown 中的链接 {#quartz4-如何处理-markdown-中的链接}

如下是 markdown 中的链接：
{{&lt; figure src="/images/manual.png" &gt;}}

我们可以使用 grep 搜索 "figure src="，可以找到处理该链接的代码：
`/home/ubuntu/quartz/quartz/plugins/transformers/oxhugofm.ts`

看到: `const figureTagRegex = new RegExp(/< ?figure src="(.*)" ?>/, "g")`
是一个正则表达式，用于匹配markdown中的链接：

接着搜索 figureTagRegex

我们可以找到：

```js
if (opts.replaceFigureWithMdImg) {
    src = src.toString()
    src = src.replaceAll(figureTagRegex, (_value, ...capture) => {
        const [src] = capture
        return `![](${src})`
    })
}
```

我们需要在生成的链接添加前缀 static ，只需更改 return 语句：

```js
if (opts.replaceFigureWithMdImg) {
    src = src.toString()
    src = src.replaceAll(figureTagRegex, (_value, ...capture) => {
        const [src] = capture
        return `![](static/${src})`
    })
}
```
