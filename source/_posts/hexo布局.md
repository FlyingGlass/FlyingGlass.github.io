---
title: hexo布局
date: 2018-03-02 17:00:55
categories: 
- [Web开发]
---

#### hexo布局

Hexo有三种默认布局：`post`、`page`和`draft`，他们分别对应不同的目录，而自定义的其他布局和`post`相同，都将存储到`source/_posts`目录。

| 布局    | 目录             |
| ------- | ---------------- |
| `post`  | `source/_posts`  |
| `page`  | `source`         |
| `draft` | `source/_drafts` |

#### 草稿

hexo的特殊布局：`draft`，这种布局建立时会被保存到`source/_drafts`文件夹，可通过`publish`命令将草稿移动到`source/_posts`目录下，该命令的使用方式与`new`类似，也可以通过`layout`指定布局。

```bash
$hexo publish [layout] <title>
```

草稿默认不会显示在页面中，可在执行时加上`--draft`参数，或是把`render_drafts`参数设置为`true`来预览草稿。

#### 参考链接

- [hexo写作](https://hexo.io/zh-cn/docs/writing.html)