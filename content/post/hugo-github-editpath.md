+++
title = "前端编辑 Hugo 文章"
description = "前端编辑 Hugo 文章"
date = "2020-03-29"
aliases = ["折腾"]
author = "飞天"
toc = true
+++


目前发文是直接在 Github 前端进行，Hugo 的构建交给 Github Actions 实现自动化部署。因此，产生个需求，文章里改个错别字什么的，能不能一键直达 GIthub 编辑页面？

```html

# Github 编辑页面链接
https://github.com/tfnick/blog/edit/master/content/posts/coding/hugo-themes.md
```

<!--more-->

想想是可行的，无非是根据当前文章的链接构建编辑页面的链接。直接放代码：

```html
<a href="https://github.com/tfnick/blog/edit/master/content/{{ replace .File.Path "\\" "/" }}" target="_blank">编辑文章</a>
```

为了页面美观，偷偷加到了某个地方。 ✌️