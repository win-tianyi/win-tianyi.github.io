# 天一机器人网站部署文档

本项目基于 [hugoplate](https://github.com/zeon-studio/hugoplate) 模板构建，使用 Hugo + Tailwind CSS，部署到 **Cloudflare Pages**，域名：**robot.tianyi.win**

## 本地开发

```bash
# 构建 CSS 并启动开发服务器（http://localhost:1313）
npm run dev

# 生产构建（输出到 public/）
npm run build
```

> 注意：本地环境若使用系统安装的 Hugo，请先执行
> `export HUGO_BIN_PATH=$HOME/.local/bin/hugo`
> 本地构建依赖已 vendor（`_vendor/` 目录），无需安装 Go。

## 更新文章

在 `content/chinese/blog/` 下新建 Markdown 文件即可，front matter 示例：

```yaml
---
title: "文章标题"
description: "文章简介"
date: 2025-03-01T10:00:00+08:00
image: "/images/image-placeholder.png"
categories: ["控制"]
author: "天一机器人"
tags: ["机械臂", "EtherCAT"]
draft: false
---
```

`draft: true` 的文章不会发布。写好后 push 到 GitHub，Cloudflare 自动构建部署。

## Cloudflare Pages 部署

### 1. 推送代码到 GitHub

```bash
git remote add origin https://github.com/<你的用户名>/<仓库名>.git
git push -u origin main
```

### 2. 在 Cloudflare 创建 Pages 项目

1. 登录 [Cloudflare Dashboard](https://dash.cloudflare.com) → **Workers & Pages** → **Create** → **Pages** → **Connect to Git**
2. 选择刚推送的 GitHub 仓库
3. 构建配置：
   - **Framework preset**: `Hugo`
   - **Build command**: `npm install && npm run build`
   - **Build output directory**: `public`
4. 环境变量（Settings → Environment variables）：
   - `NODE_VERSION`: `22`（模板要求 Node 22+）
5. **保存并部署**

> Cloudflare 构建时 `npm install` 会自动下载 hugo-extended（v0.164.0）及依赖，无需额外配置。
> 项目内已 `hugo mod vendor`，构建无需 Go 环境。

### 3. 绑定域名

1. Pages 项目 → **Custom domains** → **Set up a custom domain**
2. 输入 `robot.tianyi.win`，Cloudflare 会自动添加 DNS 记录（CNAME 指向 pages.dev 域名）
3. 等待 SSL 证书签发（约 1 分钟），即可通过 `https://robot.tianyi.win` 访问

> 前提：`tianyi.win` 域名需已托管在 Cloudflare DNS。
> 若域名在其他注册商，只需把 NS 改为 Cloudflare 提供的两个 NS 地址即可。

## 站点结构

```
content/chinese/
├── _index.md          # 首页（Banner、Features）
├── about/             # 关于我们
├── blog/              # 技术博客（文章放这里）
│   └── 机械臂零点标定.md
├── contact/           # 联系我们
└── sections/          # 首页区块（CTA、评价）
```

## 常用配置

| 文件 | 作用 |
|------|------|
| `hugo.toml` | 站点标题、baseURL、语言 |
| `config/_default/params.toml` | logo、版权、社交链接等 |
| `config/_default/menus.zh-cn.toml` | 导航菜单 |
| `i18n/zh-cn.yaml` | 界面中文文案 |
