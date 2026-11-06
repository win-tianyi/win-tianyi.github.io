# tianyi.win

TIANYI 的个人站点，基于 [Zola](https://www.getzola.org/) + [Linkita](https://github.com/salif/linkita) 主题，托管在 Cloudflare Pages。

- 线上地址：<https://tianyi.win/>
- 英文版：<https://tianyi.win/en/>
- Pages 预览域名：<https://win-tianyi-zola.pages.dev/>

## 本地开发

需要 Zola 0.23.6（主题 `min_version` 要求）：

```bash
zola serve --port 8101 --interface 127.0.0.1 --base-url http://127.0.0.1
# 打开 http://127.0.0.1:8101/
```

构建：

```bash
zola build     # 产物在 public/
```

Windows 下若没有全局 zola，可直接用 `D:\AI\web\zola.exe build`。

## 目录结构

```
content/
├── _index.md              中文首页（profile 卡片 + 文章列表）
├── _index.en.md           英文首页
├── blog/
│   ├── _index.md          Blog 分区
│   ├── _index.en.md
│   ├── hello.zh.md        中文文章
│   └── hello.en.md
└── pages/
    ├── _index.md
    ├── _index.en.md
    ├── archive.zh.md      归档页
    ├── archive.en.md
    └── projects/          项目页（bundle）
        ├── _index.md
        ├── _index.en.md
        └── cool_project/
            ├── index.zh.md
            └── index.en.md

static/        头像 profile.png、favicon 等静态资源
themes/linkita/ 主题（vendor 进仓库，Cloudflare 构建不拉 submodule）
templates/injects/head_end.html  站点级样式注入（语言按钮「English/中文」徽标）
```

## 写文章

在 `content/blog/` 下新建 `.zh.md`（中文）和 `.en.md`（英文），front matter 里写标题、日期、标签：

```toml
+++
title = "标题"
date = 2026-11-06T10:00:00Z

[taxonomies]
tags = ["随笔"]
+++
```

**注意**：分区索引（`_index.md`）在默认语言 zh 下不能带 `.zh` 后缀，Zola 会把 `_index.zh.md` 当成孤儿页；文章页、独立页面、bundle 的 `index.zh.md` 都可以带后缀。

## 多语言约定

- 中文 = `xxx.zh.md`，英文 = `xxx.en.md`，默认语言 `zh`（根路径），英文在 `/en/`
- 主题的语言按钮要求页面至少有 2 条 `link rel="alternate" hreflang`，所以只有同时存在中英译文时按钮才出现
- 菜单在 `zola.toml` 的 `[extra.menus]` 里用 `urls = { zh = "...", en = "..." }` 分语言配置（`themes/linkita/templates/components/_header_menu.html` 打过补丁支持该写法）

## 已知限制

`build_search_index = false`：Zola 内置索引基于 elasticlunr，不支持中文，开中文索引会直接构建失败。搜索按钮因此也没挂到页头。

## 部署

推送到 `main` 即触发 Cloudflare Pages 自动构建，细节见 [DEPLOY.md](DEPLOY.md)。
