---
date: 2025-05-23
tags:
  - markdown
  - hugo
  - obsidian
title: Enveloppe 插件适配 Hugo 的配置
slug: 14:18
share: true
keywords: 
description: 
series: 系列
lastmod: 
lang: cn
cover:
  image: 
author:
  - liug
dir: posts
source: https://www.printlove.cn/github-publisher-hugo/
---

> 2025-04-02 更新

先写了 [使用 Obsidian 免费建个人博客](https://www.printlove.cn/obsidian-blog/) 这篇文章，但是发现枯燥的讲解这个插件的配置不适合初学者，所以索性在这篇文章中统一整理下，对于想更深入了解的人可以选择性看看这篇。

本文基于 Obsidian 的 Enveloppe 插件，版本：v6.15.7，版本不同可能略有差异，如果差异影响使用了，我就会更新该篇文章，及时收到通知可以关注 [我的频道](https://t.me/evan_share) 。

下面我会对重要的配置进行讲解，如果你暂时不想看了，可以使用我提供的 [默认配置](https://github.com/miaogaolin/obsidian-github-publisher-hugo) 。

## File paths

![f39d2da4f8a47eb37d1154bb161f9392_MD5.webp](attachments/f39d2da4f8a47eb37d1154bb161f9392_MD5.webp) Property key 可以通过文章的属性设置上传的目录，例如我这配置：

- 通过 dir 属性获取上传的目录，最终的上传路径为 `Root folder / Property key` ，即 `content/{dir}`
- 如果 dir 属性没有设置则默认上传到 Default folder 目录下，即 content/posts

## Content

![3f3bee774db7098e774906b121648c2f_MD5.png](attachments/3f3bee774db7098e774906b121648c2f_MD5.png)

Text replacer

每行后面都有个箭头，↓箭头表示插件应用其它配置之前替换，↑箭头表示之后替换。

### \[\[Wikilinks\]\] 转 \[MDlinks\](links)

开启 Content -> `[[Wikilinks]]` 转 `[MDlinks](links)` 菜单。

![78c6812d4aaf8d32f7cb2e961ff3a885_MD5.webp](attachments/78c6812d4aaf8d32f7cb2e961ff3a885_MD5.webp)

### 图片

在 Obsidian 中引用本地图片链接时是不需要路径的，而在 Hugo 中一般会把图片统一放在 /static/images 目录下。

因此在 Hugo 文章的引用方式路径为 `/images/图片名称` 。

> 我图片在本地存储的，如果你有图床那这个配置也没那么重要

现在要做的就是给 Obsidian 的本地图片路径增加 /images 前缀，使用到了 Text replacer 正则。

支持 `![]()` 的图片格式，箭头 ↑ 表示会等 `![[]]` 转化成 `![]()` 之后执行：

```javascript
匹配内容：/\]\(([^/\)]+?)\.(png|jpg|jpeg|webp|gif|avif)/
替换：](/images/$1.$2
箭头：↑
```

支持 `![[]]` 的图片格式，且增加了 caption 和 size 的支持，分两种情况：

```lua
情况一，支持 ![[filename.png|size]] 
匹配内容：/\!\[\[([^/\]]+?)\.(png|jpg|jpeg|webp|gif|avif)\|(\d+)(x(\d+))?\]\]/
替换：{{< figure src="/images/$1.$2"  width="$3" height="$5">}}
箭头：↓

情况二，支持 ![[filename.png|caption|size]]
匹配内容：/\!\[\[([^/\]]+?)\.(png|jpg|jpeg|webp|gif|avif)\|([^\|]*?)(\|(\d+)(x(\d+))?)?\]\]/
替换：{{< figure src="/images/$1.$2" caption="$3" width="$5" height="$7">}}
箭头：↓
```

上面的两种图片格式都支持，区别就是 `![[]]` 格式会支持图片的 caption 和 size。

### 内部文章引用

在 Obsidian 中文章的之间的引用使用 `[[文章名]]` 用法，而在 Hugo 中用的是 `{{< relref "文章名.md" >}}` 语法。

Text replacer 正则：

```bash
匹配内容：/\]\(([^)\.]+)\.md/
替换：]({{< relref "$1".md >}}
箭头：↑
```

在 Obsidian 文章之间引用是没有 md 后缀的，因此执行该替换的时间是等文章使用插件转化后再执行，据此后箭头向下。

### 文章锚点

如果文章链接中出现了锚点跳转，则记着下拉框选择 Lower： ![06f2d8b2cd2b782a98471d3d9ef4e928_MD5.webp](attachments/06f2d8b2cd2b782a98471d3d9ef4e928_MD5.webp)

> 锚点跳转就是跳转到文章的某个标题处，在 Obsidian 的格式为 `[[文章#标题]]`

该配置会将锚点 (`#标题`) 处理：

1. 转小写
2. 空格转化减号 (`-`)

只有转化才能正常访问锚点。

### 属性一级转二级

在 Obsidian 中如果属性是二级的话，展示效果就非常不友好直观，所以我会在 Obsidian 里这样写需要转二级的属性：

```yaml
---
cover.image: 
---
```

这个属性我用来设置文章的封面，具体要看看自己的主题是不是这样，如果不是那需要自己调整下。

Text replacer 正则：

```coffeescript
匹配内容：/cover\.image/
替换：cover:\n    image
箭头：↑
```

替换中二级的缩紧正常应该使用 `\t` ，但我测试发现无效就改用 4 个空格。

### 删除留坑标记

当我发布的文章中如果许诺了一件事情，我就会配合 Obsidian 的 dataview 插件写一个标记。

然后使用 dataview 插件写的仪表盘里展示出留了哪些坑？

![de1164044173be4b2f2e5436e890ea92_MD5.webp](attachments/de1164044173be4b2f2e5436e890ea92_MD5.webp)

坑列表示意图

标记格式（todo1 记着改成 todo，我不想识别到这个标记，只是演示而已）：
```text
[todo1:: 留坑的内容]
```

dataview 语法：

现在使用 Text replacer 正则删除文章里的标记：

```coffeescript
匹配内容：/\[todo::[^\]]+?\]/
替换这块不填，就会删除匹配到的内容
箭头：↑
```

### 标签

在 Obsidian 里是支持 `/` 方式渲染多级标签的，在 Hugo 中是不支持的，所以需要开启将 `/` 转位 `_` 。 ![20a03063ce4157cda5642b86268dd7e4_MD5.webp](attachments/20a03063ce4157cda5642b86268dd7e4_MD5.webp)

### 踩的坑

Markdown hard line break 最好关闭，我这边开启造成了空行变多，使代码难看、表格无法渲染。

## Attachment & embed note config

默认 Transfer attachments 会开启附件上传的，重点是需要使用 Default attachment folder 设置附近的上传目录。

![5b40f8763019a9aeb2fd864b1e2f3f65_MD5.webp](attachments/5b40f8763019a9aeb2fd864b1e2f3f65_MD5.webp)

## Issues

这是我给作者提交的问题，现在都已经修复。

- [正则表达式替换内容不支持转义字符](https://github.com/ObsidianPublisher/obsidian-github-publisher/issues/254)
- [锚点不支持中文](https://github.com/ObsidianPublisher/obsidian-github-publisher/issues/285)
- [Obsidian属性或FrontMatter重复](https://github.com/ObsidianPublisher/obsidian-github-publisher)