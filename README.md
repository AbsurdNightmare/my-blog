# 我的博客

用 [Hugo](https://gohugo.io/) + [Ladder 主题](https://github.com/guangzhengli/hugo-theme-ladder) 搭建，
源码放在 GitHub，推送到 `master` 后由 GitHub Actions 自动构建并发布到
<https://absurdnightmare.github.io/>。

---

## 一、本地预览

改完东西想看效果，在项目根目录跑：

```bash
hugo server -D
```

然后浏览器打开 <http://localhost:1313>。

- 加 `-D` 才会显示草稿（`draft = true` 的文章）。平时写文章建议都加上。
- 改文件会自动刷新，不用重启。
- `Ctrl + C` 停止。

只想构建不预览：

```bash
hugo
```

产物在 `public/`，这个目录已经在 `.gitignore` 里，**不需要手动提交**。

---

## 二、写一篇新文章

### 1. 创建文件

```bash
hugo new content blog/我的文章标题.md
```

文件名建议用英文小写加连字符，它同时也是网址的一部分。比如
`content/blog/go-slice-tricks.md` 的地址就是 `/blog/go-slice-tricks/`。

> 「随笔」栏目的文章把 `blog` 换成 `art`：
> `hugo new content art/某个下午.md`

### 2. 编辑 front matter

命令会生成这样一个文件头：

```toml
+++
date = '2026-09-17T14:26:37+08:00'
draft = true
title = 'Test Post'
summary = ''
tags = []
# series = []
# featured = false
+++
```

各字段的意思：

| 字段 | 说明 |
| --- | --- |
| `title` | 文章标题，显示在列表和网页标题上 |
| `date` | 发布时间，**列表和归档按它倒序排列**。想置顶就把时间写晚一点 |
| `draft` | `true` 是草稿，线上不会出现；写完了改成 `false` |
| `summary` | 摘要，显示在列表页标题下面。留空则自动截取正文开头 |
| `tags` | 标签，可以写多个：`tags = ['Go', '并发']` |
| `series` | 系列名。同一个系列的文章会在文末互相推荐，需要**至少两篇**才显示。不需要就删掉这行 |
| `featured` | 设为 `true` 会出现在首页「推荐阅读」里 |
| `math` | 设为 `true` 才能在文章里写 LaTeX 公式 |

### 3. 写正文

标题用 `##`、`###`，左侧目录会自动生成。

### 4. 发布

把 `draft` 改成 `false`，然后：

```bash
git add .
git commit -m "新文章：我的文章标题"
git push
```

推上去之后 GitHub Actions 会自动构建，一两分钟后线上就能看到了。
在 <https://github.com/AbsurdNightmare/AbsurdNightmare.github.io/actions> 可以看构建进度。

---

## 三、排版功能速查

`content/blog/markdown-cheatsheet.md` 是一篇可以直接照着抄的示例文章，包含所有下面这些用法。

**代码块** —— 三个反引号加语言名，自动高亮，右上角有复制按钮：

````markdown
```go
fmt.Println("hello")
```
````

**折叠块** —— 适合放答案或很长的报错：

```markdown
{{</* toggle summary="点开看答案" */>}}
被折叠的内容
{{</* /toggle */>}}
```

**标签页**：

```markdown
{{</* tabgroup */>}}
{{</* tab name="Go" */>}}
内容一
{{</* /tab */>}}
{{</* tab name="Python" */>}}
内容二
{{</* /tab */>}}
{{</* /tabgroup */>}}
```

**数学公式** —— 先在 front matter 里加 `math = true`，然后：

```markdown
行内：$O(n \log n)$

独立一行：
$$
\sum_{i=1}^{n} i = \frac{n(n+1)}{2}
$$
```

**图片** —— 把图片文件放到 `static/images/` 下，然后：

```markdown
![图片说明](/images/文件名.png)
```

**摘要** —— 优先级：front matter 的 `summary` > 正文里的 `<!--more-->` > 自动截取。

---

## 四、改站点信息

站点配置全在根目录的 `hugo.toml` 里：

| 想改什么 | 改哪里 |
| --- | --- |
| 首页大标题 | `params.author` |
| 首页标题下的小字 | `params.authorDescription` |
| 首页自我介绍段落 | `params.info` |
| 首页头像 | 替换 `static/images/avatar.png`（建议正方形） |
| 浏览器标签页图标 | 替换 `static/images/avatar.png`，或改 `params.favicon` |
| 导航栏左上角文字 | `params.brand` |
| 导航栏菜单项 | `[menu]` 下面的 `[[menu.main]]`，`weight` 决定顺序 |
| 导航栏右侧的图标 | `[[params.social]]`，照着已有的 GitHub 那条复制一份改 `url` 和 `pre` |
| 每页显示几篇文章 | `[pagination] pagerSize` |

改完 `hugo server -D` 重新看一眼即可。

---

## 五、目录结构

```
blog/
├── hugo.toml              # 站点配置（标题、菜单、头像、分页……都在这）
├── content/               # 所有内容，你平时只需要动这里
│   ├── blog/              # 「博客」栏目
│   ├── art/               # 「随笔」栏目
│   ├── about.md           # 「关于」页
│   ├── archives.md        # 「归档」页
│   └── tags/_index.md     # 「标签」页
├── archetypes/            # 新建文章时的模板
├── layouts/               # 覆盖主题的模板（一般不用动）
│   ├── _default/rss.xml       # RSS 订阅
│   ├── _default/archives.html # 归档页（月份显示成「9月」）
│   └── partials/blog/         # 文章元信息、系列推荐
├── i18n/zh.toml           # 界面中文文案，想改「推荐阅读」这类字就改这里
├── static/images/         # 图片、头像
└── themes/hugo-theme-ladder/  # 主题（git submodule，别直接改）
```

`layouts/` 和 `i18n/` 里的文件是**覆盖**主题用的：主题升级后这些改动不会丢。

---

## 六、几个坑（已经踩过了）

**别直接改 `themes/hugo-theme-ladder/` 里的文件。** 它是 git submodule，
主题更新时你的修改会被覆盖。要改就在根目录的 `layouts/` 下建同名文件。

**文章必须放在 `content/blog/` 或 `content/art/` 下。** 主题的模板是按这两个栏目
写死的，放到别的地方（比如 `content/posts/`）不会显示在列表里。

**语言代码要保持 `zh`。** `hugo.toml` 里的 `defaultContentLanguage` 必须和
`themes/hugo-theme-ladder/i18n/zh.toml` 对得上，写成 `zh-cn` 的话界面上
「字数」「阅读时间」这些会全部变成空白。

**中文标签的网址会被编码。** `/tags/生活/` 在浏览器地址栏里会显示成
`/tags/%E7%94%9F%E6%B4%BB/`，这是正常的，不影响使用。

**克隆仓库时记得带上主题：**

```bash
git clone --recurse-submodules <仓库地址>
```

已经克隆过但 `themes/` 是空的，就补一条：

```bash
git submodule update --init --recursive
```
