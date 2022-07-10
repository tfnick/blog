+++
title = "Hugo 图片懒加载"
description = "Hugo 图片懒加载"
date = "2020-03-24"
aliases = ["折腾"]
author = "飞天"
toc = true
+++


起因，想给文章插入的 img 图片添加 `loading='lazy'`，果然有爱折腾的，看（[这里](https://lvv.me/posts/2019/12/24_hugo_render_hooks/)），官方说明 [#render-hook-templates](https://gohugo.io/getting-started/configuration-markup/#render-hook-templates)，Hugo v0.62+

简单说能对md内的链接（link）和图像（image）自定义渲染，比如实现以上需求，只需在主题新建 `layouts/_default/_markup/render-image.html` 

<!--more-->

```html
layouts/_default
├── _markup
│   └── render-image.html
```

丢入代码：
```html
<p class="md_image">
  <img loading='lazy' src="{{ .Destination | safeURL }}" alt="{{ .Text }}" {{ with .Title}} title="{{ . }}"{{ end }} />
</p>
```

🐯🐶一下，收工！等等，这么简单？！再来个图片自动 jsdelivr cdn 前缀？

