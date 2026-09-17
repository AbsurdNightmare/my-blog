+++
date = '2026-09-16T20:00:00+08:00'
draft = false
title = '写作速查：Markdown 和本站支持的排版功能'
summary = '一份给自己看的速查表：代码块、折叠、标签页、公式、图片该怎么写。'
tags = ['Hugo', 'Markdown']
series = ['博客搭建']
# 想在文章里写 LaTeX 公式，就把下面这行取消注释
math = true
+++

写文章的时候想不起来某个语法怎么写，就翻这一页。**你可以直接照着这个文件的源码改。**

## 标题与目录

用 `##` 到 `######` 写小标题。文章左侧的目录会自动根据 `##`、`###` 生成，不用手动维护。

## 文字样式

`**加粗**`、`*斜体*`、`` `行内代码` ``、~~删除线~~、[链接](https://gohugo.io/)。

> 引用：把一段话来出来。
> 可以写多行。

## 代码块

三个反引号加上语言名，就会有语法高亮和右上角的复制按钮：

```go
func main() {
    msg := "hello, world"
    fmt.Println(msg)
}
```

```bash
hugo new content blog/my-new-post.md
hugo server -D
```

## 列表

1. 有序列表
2. 第二项

- 无序列表
- 第二项
  - 嵌套一层

## 表格

| 命令 | 作用 |
| --- | --- |
| `hugo server -D` | 本地预览，包含草稿 |
| `hugo` | 构建到 `public/` |

## 折叠块

适合放「答案」「长报错信息」这种不想一上来就占满屏幕的内容：

{{< toggle summary="点开看答案" >}}
把 `draft: true` 改成 `false`，或者本地预览时加 `-D` 参数。
{{< /toggle >}}

## 标签页

{{< tabgroup >}}
{{< tab name="Go" >}}
```go
fmt.Println("hello")
```
{{< /tab >}}
{{< tab name="Python" >}}
```python
print("hello")
```
{{< /tab >}}
{{< /tabgroup >}}

## 数学公式

需要在 front matter 里加上 `math = true`，然后就能用 LaTeX 语法：

$$
\sum_{i=1}^{n} i = \frac{n(n+1)}{2}
$$

行内公式写成这样：$O(n \log n)$。

## 图片

把图片放到和文章同一个目录下的文件夹里，或者统一放到 `static/images/` 下：

```markdown
![图片说明](/images/example.png)
```

## 摘要怎么写

三种方式，优先级从高到低：

1. front matter 里的 `summary = '...'`
2. 正文里写 `<!--more-->`，它前面的内容就是摘要
3. 什么都不写，Hugo 自动截取开头一段

## 系列文章

front matter 里写上 `series = ['博客搭建']`，同一个系列的文章会自动在文末互相推荐 ——
比如你现在滑到最下面，就能看到这个系列里的其他文章。
